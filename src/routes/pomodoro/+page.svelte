<script>
  import ProgressBar from '../../components/ProgressBar.svelte';
  let workprogress = $state(0);
  let breakprogress = $state(0);
  let workseconds = $state(1500);
  let breakseconds = $state(300);
  let worktimer = $state(undefined);
  let breaktimer = $state(undefined);
  let workrounds = $state(0);
  let breakrounds = $state(0);
  
  let showWorkInfo = true;
  function toggleWork () {
    showWorkInfo = !showBreakInfo;
  }
  let showBreakInfo = true;
  function toggleBreak () {
    showBreakInfo = !showBreakInfo;
  }


  function startTimer() {
    if(workseconds <= 0) {
      breakTimer();
      toggleWork();
    } else {
      workTimer();
    }
  }

  function workTimer() {
    clearInterval(breaktimer);
    worktimer = setInterval(() => {
      if(workseconds <= 0){
        resetBreakTimer();
        breakTimer();
        workrounds++;
        toggleBreak();
      } else {
        workseconds--;
        workprogress += 1/15;
      }
    }, 1000);
  } 

  function breakTimer() {
    clearInterval(worktimer);
    breaktimer = setInterval(() => {
      if(breakseconds <= 0) {
        resetWorkTimer();
        workTimer();
        breakrounds++;
        toggleWork();
      } else {
        breakseconds--;
        breakprogress += 1/3;
      }
    }, 1000)
  }

  function stopTimer() {
    clearInterval(worktimer);
    clearInterval(breaktimer);
  }

  function resetWorkTimer() {
    workseconds = 1500;
    workprogress = 0;
  }
  function resetBreakTimer() {
    breakseconds = 300;
    breakprogress = 0;
  }

  function resetTimer() {
    workseconds = 1500;
    workprogress = 0;
    breakseconds = 300;
    breakprogress = 0;
  }

</script>



<div class="faustina p-3 flex justify-center text-6xl bg-error text-black">Pomodoro</div>
<hr/>
<div class="montserrat text-xl font-semibold flex justify-around bg-accent bg-gradient-to-br from-secondary to-base-300">
  <button class="btn btn-secondary border-primary m-2 rounded-xl" onclick={startTimer}>Start Timer</button>
	<div class="m-3">•</div>
  <button class="btn btn-secondary border-primary m-2 rounded-xl" onclick={stopTimer}>Stop Timer</button>
	<div class="m-3">•</div>
  <button class="btn btn-secondary border-primary m-2 rounded-xl" onclick={resetTimer}>Reset Timer</button>
  <div class="m-3">•</div>
  <button class="btn btn-secondary border-primary m-2 rounded-xl" onclick={resetTimer}>Customize Timer</button>
</div>

<hr/>

<div class="m-3">
  <button class="btn btn-accent border-primary m-2 rounded-xl">Show Progress Bar</button>
  <button class="btn btn-accent border-primary m-2 rounded-xl">Hide Progress Bar</button>
</div>

{#if showWorkInfo == true}
<div class="m-3">
  <div>You have {parseInt(workseconds / 60)} minutes and {workseconds % 60} seconds left until your next break.</div>
  <ProgressBar color={'bg-error'} current={workprogress} max={100} />
</div>
{/if}

{#if showBreakInfo == true}
<div class="m-3">
  <div>Break time! You have {parseInt(breakseconds / 60)} minutes and {breakseconds % 60} seconds left until your next work session. Try to avoid checking social media.</div>
  <ProgressBar color={'bg-success'} current={breakprogress} max={100} />
</div>
{/if}

<br/>
<div class="m-3">You have been studying for {(workrounds * 25) + parseInt(workprogress * 1/4)} minutes and have taken {(breakrounds * 5) + parseInt(breakprogress * 1/20)} minutes worth of breaks. Study for another _ minutes to unlock a minigame!</div>


