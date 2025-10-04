<div class="bg-gradient-to-br from-success to-green-800/50 faustina text-6xl text-black flex justify-center p-3">Task Checklist</div>
<hr/>

<script>
  let categories = $state(["Category 1", "Category 2", "Category 3"]);
  

  let todos = $state([
    {inputSubTodo: "", id: 1, name: "Here's an example of a task", numPomodoros: 3, isComplete: false, dueDate: '', importance: 1, subTodos: [{subName: "Here's a subtask"}, {subName: "And here's another"}],
    category: "Category 1"
    },

    {inputSubTodo: "", id: 2, name: "Try changing the number of pomodoros this one has", numPomodoros: 2, isComplete: false, dueDate: '', importance: 2, subTodos: [], category: "Category 2"},
    {inputSubTodo: "", id: 3, name: "Now try hitting the checkboxes and add some of your own tasks", numPomodoros: 1, isComplete: false, dueDate: '', importance: 3, subTodos: [],category: "Category 3"},
  ]);
  let todosNotComplete = $derived(todos.filter(v => v.isComplete == false));
  let inputTxt = $state('');
  let subInputTxt = $state('');
  let inputDate = $state('');
  let inputImportance = $state('');
  let inputSubTodos = $state([]);
  let inputCategory = $state('');

  function add() {
    todos.push({inputSubTodo: "", name: inputTxt, numPomodoros: 0, isComplete: false, id: Date.now(), dueDate: inputDate, importance: inputImportance, subTodos: inputSubTodos, category: inputCategory});
    inputTxt = '';
    inputDate = '';
    inputImportance = '';
    inputSubTodos = [];
    inputCategory = ''
  }

  function subAdd(id) {
    for(let i = 0; i < todos.length; i++) {
      if(todos[i].id === id){
        todos[i].subTodos.push({subName: todos[i].inputSubTodo});
        todos[i].inputSubTodo = '';
      }
    }
  }

  function addP(id) {
    for(let i=0; i<todos.length; i++) {
      if (todos[i].id === id) {
        todos[i].numPomodoros++;
        break;
      }
    }
  }

  function subP(id) {
    for(let i=0; i<todos.length; i++) {
      if (todos[i].id === id) {
        if (todos[i].numPomodoros == 0) return;
        else {
          todos[i].numPomodoros--;
        }
        break;
      }
    }
  }

  let prodScore = $state(0);
  let numTasksCompleted = $state(0);
  let punctualCompleted = $state(0);
  let punctuality = $state(1);
  
  let timeSpentWorking = $state(0);

  function markComplete(id) {
    for(let i=0; i<todos.length; i++) {
      if (todos[i].id === id) {
        todos[i].isComplete = true;
        numTasksCompleted++;
        let imp = todos[i].importance + 1;
        let subs = todos[i].subTodos.length;
        let estTime = $state(todos[i].numPomodoros * 25);
        if(todos[i].subTodos.length == 0){
          subs = 1;
        }
        if(todos[i].numPomodoros == 0){
          estTime = 25;
        }
        if(todos[i].dueDate > formattedToday){
          prodScore += (4 * imp * subs * estTime);
          punctualCompleted++;
        } else if(todos[i].dueDate == formattedToday || todos[i].dueDate == ''){
          prodScore += (2 * imp * subs * estTime);
          punctualCompleted++;
        } else {
          prodScore += (imp * subs * estTime);
        }
        punctuality = (punctualCompleted/numTasksCompleted).toFixed(3);
        break;
      }
    }
  }

  function deleteTask(id) {
    for(let i=0; i < todos.length; i++){
      if(todos[i].id === id){
        todos[i].isComplete = true;
      }
    }
  }

  function flag(id) {
    for(let i=0; i<todos.length; i++){
      if(todos[i].id === id) {
        todos[i].importance = (todos[i].importance + 1) % 4;
        break;
      }
    }
  }

  function unflag(id) {
    for(let i=0; i<todos.length; i++){
      if(todos[i].id === id) {
        if(todos[i].importance === '') return;
        else {
          todos[i].importance = '';
        }
        break;
      }
    }
  }

  const today = new Date();
  let formattedToday = today.getFullYear() + '-' + (today.getMonth() + 1) + '-0' + today.getDate();

  import ProgressBar from '../../components/ProgressBar.svelte';

  // iw is short for "initial work [timer length]"; ib is short for "initial break [timer length]". These stand in for the values of 1500 and 300 seconds, respectively, in order to make test cases easier to check.

  let iw = 1500;
  let ib = 300;

  let workprogress = $state(0);
  let breakprogress = $state(0);
  let workseconds = $state(iw);
  let breakseconds = $state(ib);
  let worktimer = $state(undefined);
  let breaktimer = $state(undefined);

  let activeTask = $state(undefined);
  let running = $state(undefined);
  let working = $state(undefined);

  function startTimer(id) {
    activeTask = id;
    if(workseconds <= 0) {
      breakTimer();
    } else {
      workTimer();
    }
  }

  function workTimer() {
    clearInterval(breaktimer);
    running = true;
    working = true;
    worktimer = setInterval(() => {
      if(workseconds <= 0){
        resetBreakTimer();
        breakTimer();
      } else {
        workseconds--;
        workprogress += 100/iw;
      }
    }, 1000);
  } 

  function breakTimer() {
    clearInterval(worktimer);
    running = true;
    working = false;
    breaktimer = setInterval(() => {
      if(breakseconds <= 0) {
        resetWorkTimer();
        workTimer();
      } else {
        breakseconds--;
        breakprogress += 100/ib;
      }
    }, 1000)
  }

  function stopTimer() {
    clearInterval(worktimer);
    clearInterval(breaktimer);
    running = false;
  }

  function resetWorkTimer() {
    workseconds = iw;
    workprogress = 0;
    running = false;
  }
  function resetBreakTimer() {
    breakseconds = ib;
    breakprogress = 0;
    running = false;
  }

  function resetTimer() {
    workseconds = iw;
    workprogress = 0;
    breakseconds = ib;
    breakprogress = 0;
    stopTimer();
  }

  function hidePopup() {
    activeTask = false;
    stopTimer();
  }  
</script>
<div class="bg-gradient-to-br from-base-content via-neutral to-primary">
  <input 
    class="bg-secondary text-black p-2 m-2 rounded-xl border focus:outline-none focus:ring-2 focus:ring-primary" 
    placeholder="Add a new task..."
    bind:value={inputTxt} />
  
  <input
      bind:value={inputDate}
      placeholder="Pick Due Date"
      class="bg-secondary text-black p-2 m-2 rounded-xl border focus:outline-none focus:ring-2 focus:ring-primary" 
      type="date" />

  <select class="select select-secondary bg-secondary text-black p-2 m-2 rounded-xl border focus:outline-none focus:ring-2 focus:ring-primary" bind:value={inputImportance}>
    <option value={''} disabled selected>Task Importance</option>
    <option value={1}>★</option>
    <option value={2}>★★</option>
    <option value={3}>★★★</option>
  </select>

  <select class="select select-secondary bg-secondary text-black p-2 m-2 rounded-xl border focus:outline-none focus:ring-2 focus:ring-primary"
  bind:value={inputCategory}
  >
    <option value={''} disabled selected>Categorize</option>
    {#each categories as c}
      <option value={c}>{c}</option>
    {/each}
  </select>
  

  <button 
    class="bg-secondary border rounded-xl p-2 m-2 hover:bg-primary font-semibold disabled:text-gray-500" 
    onclick={add}
    disabled={inputTxt.length == 0}
    >Add Task</button>
</div>
<hr />



<ul class="p-1 m-1">
  {#each todosNotComplete as v, i}
  <li>
    <div class="font-bold {`${v.category == '' && "hidden"}`}">[{v.category}]</div>

    <input 
      type="checkbox" 
      checked="{v.isComplete}"
      class="checkbox checkbox-primary"
      onclick="{() => markComplete(v.id)}"
      />
    <button class="h-8 w-16 bg-accent font-semibold border rounded-xl hover:bg-blue-300 p-1 m-0.5 disabled:text-gray-500" onclick={() => flag(v.id)}>
      {new Array(v.importance).fill('★').join("")}
    </button>

    {v.name} ({new Array(v.numPomodoros).fill('🍅').join('')}
    <button 
      class="bg-secondary border rounded-xl hover:bg-primary p-1 m-0.5"
      onclick={() => addP(v.id)}>+🍅</button>

    <button 
      class="bg-secondary border rounded-xl hover:bg-primary p-1 m-0.5"
      onclick={() => subP(v.id)}>-🍅</button>)

    <div class="pl-5 m-0.5">
      {#each v.subTodos as subv, subi}
      <li>
        <input type="checkbox" class="checkbox checkbox-primary"/>
        {subv.subName}
      </li>
      {/each}
    </div>

    <div class="{`${v.dueDate == '' && "hidden"}`} {`${v.dueDate <= formattedToday && "text-error font-semibold"}`}">Due: {v.dueDate}</div>

    <div class="text-sm">
      <button 
        class="bg-success font-semibold border rounded-xl hover:bg-green-300 p-1 m-0.5 disabled:text-gray-500" onclick={() => startTimer(v.id)} disabled={running == true}>Start Timer</button>
      
      <input 
        class="bg-info text-black p-1 m-0.5 rounded-xl border focus:outline-none focus:ring-2 focus:ring-primary" 
        placeholder="Add a new subtask..."
        bind:value={v.inputSubTodo} />

      <button class="bg-info font-semibold border rounded-xl hover:bg-purple-300 p-1 m-0.5 disabled:text-gray-500" onclick={() => subAdd(v.id)} disabled={v.inputSubTodo.length == 0}>Add Subtask</button>

      <button class="bg-error font-semibold border rounded-xl hover:bg-red-300 p-1 m-0.5" onclick={() => deleteTask(v.id)}>Delete Task</button>

      
    </div>

    <div class={`${activeTask !== v.id && "hidden"} card bg-secondary text-primary-content w-120 m-3 border border-2 border-accent`}>
      <div class="card-body">
        <div class="card-actions justify-end">
        <button class="btn btn-square btn-sm bg-primary border border-accent" onclick={hidePopup}>
          <svg
            xmlns="http://www.w3.org/2000/svg"
            class="h-6 w-6"
            fill="none"
            viewBox="0 0 24 24"
            stroke="currentColor">
            <path
              stroke-linecap="round"
              stroke-linejoin="round"
              stroke-width="2"
              d="M6 18L18 6M6 6l12 12" />
          </svg>
        </button>
        </div>

        <div class={`${working !== true && "hidden"}`}>
          <h2 class="card-title">Task Started!</h2>
          <div>You have {parseInt(workseconds / 60)} minutes and {workseconds % 60} seconds left until your next break.</div>

          <ProgressBar color={'bg-error'} current={workprogress} max={100} />
        </div>

        <div class={`${working === true && "hidden"}`}>
          <h2 class="card-title">Break Time!</h2>
          <div>You have {parseInt(breakseconds / 60)} minutes and {breakseconds % 60} seconds left until your next work session. Try to avoid checking social media.</div>

          <ProgressBar color={'bg-success'} current={breakprogress} max={100} />
        </div>

        <div class="card-actions justify-end">
          <button 
            class="bg-warning font-semibold border rounded-xl hover:bg-yellow-300 p-1 m-0.5" onclick={stopTimer}>Stop Timer</button>
          <button 
            class="bg-error font-semibold border rounded-xl hover:bg-red-300 p-1 m-0.5" onclick={resetTimer}>Reset Timer</button>
        </div>
      </div>
    </div>
    <hr class="mt-2 mb-2"/>
  </li>
    
  {/each}
</ul>

<div class="p-2 m-2 border border-3 border-accent flex justify-around">
  <div>Tasks Completed: {numTasksCompleted}</div>
  <div class="tooltip" data-tip="To see how your productivity score is calculated, see How to Use">Productivity Score: {prodScore}</div>
  <div class="tooltip" data-tip="The number of tasks you have done on or ahead of time divided by the total number of tasks you have completed">Punctuality: {punctuality * 100}%</div>
</div>