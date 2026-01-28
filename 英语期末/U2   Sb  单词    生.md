mestamp, size, code, inliningId, scriptOffset, bailoutType,
      sourcePositionText, deoptReasonText) {
    this.profile_.deoptCode(timestamp, code, inliningId, scriptOffset,
      bailoutType, sourcePositionText, deoptReasonText);
  }

  processCodeMove(from, to) {
    this.profile_.moveCode(from, to);
  }

  processCodeDelete(start) {
    this.profile_.deleteCode(start);
  }

  processCodeSourceInfo(
      start, script, startPos, endPos, sourcePositions, inliningPositions,
      inlinedFunctions) {
    this.profile_.addSourcePositions(start, script, startPos,
      endPos, sourcePositions, inliningPositions, inlinedFunctions);
  }

  processScriptSource(script, url, source) {
    this.profile_.addScriptSource(script, url, source);
  }

  processSFIMove(from, to) {
    this.profile_.moveSharedFunctionInfo(from, to);
  }

  includeTick(vmState) {
    if (this.stateFilter_ !== null) {
      return this.stateFilter_ == vmState;
    } else if (this.runtimeTimerFilter_ !== null) {
      return this.currentRuntimeTimer == this.runtimeTimerFilter_;
    }
    return true;
  }

  processRuntimeTimerEvent(name) {
    this.currentRuntimeTimer = name;
  }

  processTick(
        pc, ns_since_start, is_external_callback, tos_or_external_callback,
        vmState, stack) {
    this.distortion += this.distortion_per_entry;
    ns_since_start -= this.distortion;
    if (ns_since_start < this.range_start || ns_since_start > this.range_end) {
      return;
    }
    this.ticks_.total++;
    if (vmState == TickProcessor.VmStates.GC) this.ticks_.gc++;
    if (!this.includeTick(vmState)) {
      this.ticks_.excluded++;
      return;
    }
    if (is_external_callback) {
      // Don't use PC when in external callback code, as it can point
      // inside callback's code, and we will erroneously report
      // that a callback calls itself. Instead we use tos_or_external_callback,
      // as simply resetting PC will produce unaccounted ticks.
      pc = tos_or_external_callback;
      tos_or_external_callback = 0;
    } else if (tos_or_external_callback) {
      // Find out, if top of stack was pointing inside a JS function
      // meaning that we have encountered a frameless invocation.
      const funcEntry = this.profile_.findEntry(tos_or_external_callback);
      if (!funcEntry || !funcEntry.isJSFunction || !funcEntry.isJSFunction()) {
        tos_or_external_callback = 0;
      }
    }

    this.profile_.recordTick(
      ns_since_start, vmState,
      this.processStack(pc, tos_or_external_callback, stack));
  }

  advanceDistortion() {
    this.distortion += this.distortion_per_entry;
  }

  processHeapSampleBegin(space, state, ticks) {
    if (space != 'Heap') return;
    this.currentProducerProfile_ = new CallTree();
  }

  processHeapSampleEnd(space, state) {
    if (space != 'Heap' || !this.currentProducerProfile_) return;

    print(`Generation ${this.generation_}:`);
    const tree = this.currentProducerProfile_;
    tree.computeTotalWeights();
    const producersView = this.viewBuilder_.buildView(tree);
    // Sort by total time, desc, then by name, desc.
    producersView.sort((rec1, rec2) =>
      rec2.totalTime - rec1.totalTime ||
      (rec2.internalFuncName < rec1.internalFuncName ? -1 : 1));
    this.printHeavyProfile(producersView.head.children);

    this.currentProducerProfile_ = null;
    this.generation_++;
  }

  printVMSymbols() {
    console.log(
      JSON.stringify(this.profile_.serializeVMSymbols()));
  }

  printStatistics() {
    if (this.preprocessJson) {
      this.profile_.writeJson();
      return;
    }

    print(`Statistical profiling result from ${this.lastLogFileName_}` +
      `, (${this.ticks_.total} ticks, ${this.ticks_.unaccounted} unaccounted, ` +
      `${this.ticks_.excluded} excluded).`);


    if (this.ticks_.total == 0) return;

    const flatProfile = this.profile_.getFlatProfile();
    const flatView = this.viewBuilder_.buildView(flatProfile);
    // Sort by self time, desc, then by name, desc.
    flatView.sort((rec1, rec2) =>
      rec2.selfTime - rec1.selfTime ||
      (rec2.internalFuncName < rec1.internalFuncName ? -1 : 1));
    let totalTicks = this.ticks_.total;
    if (this.ignoreUnknown_) {
      totalT