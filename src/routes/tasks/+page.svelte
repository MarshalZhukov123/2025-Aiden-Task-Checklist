<script>
	let categories = $state(['Home', 'Work', 'School']);

	let todos = $state([
		{
			inputSubTodo: '',
			id: 1,
			name: "Here's an example of a task",
			numPomodoros: 3,
			isComplete: false,
			dueDate: '',
			importance: 1,
			subTodos: [{ subName: "Here's a subtask" }, { subName: "And here's another" }],
			category: 'Example Tag 1'
		},

		{
			inputSubTodo: '',
			id: 2,
			name: 'Try changing the number of pomodoros this one has',
			numPomodoros: 2,
			isComplete: false,
			dueDate: '',
			importance: 2,
			subTodos: [],
			category: 'Example Tag 2'
		},
		{
			inputSubTodo: '',
			id: 3,
			name: 'Now try hitting the checkboxes and add some of your own tasks',
			numPomodoros: 1,
			isComplete: false,
			dueDate: '',
			importance: 3,
			subTodos: [],
			category: 'Example Tag 3'
		}
	]);
	let todosNotComplete = $derived(todos.filter((v) => v.isComplete == false));
	let inputTxt = $state('');
	let subInputTxt = $state('');
	let inputDate = $state('');
	let inputImportance = $state('');
	let inputSubTodos = $state([]);
	let inputCategory = $state('');

	onMount(() => {
		let t = localStorage.getItem('tags');

		if (t !== null) {
			t = JSON.parse(t).map((v) => v.name);
			categories = [...categories, ...t];
		}
	});

	onMount(() => {
		let tasks = localStorage.getItem('todos');

		if (tasks !== null) {
			todos = JSON.parse(tasks);
		}
	});

	function add() {
		todos.push({
			inputSubTodo: '',
			name: inputTxt,
			numPomodoros: 0,
			isComplete: false,
			id: Date.now(),
			dueDate: inputDate,
			importance: inputImportance,
			subTodos: inputSubTodos,
			category: inputCategory
		});
		inputTxt = '';
		inputDate = '';
		inputImportance = '';
		inputSubTodos = [];
		inputCategory = '';
		localStorage.setItem('todos', JSON.stringify(todos));
	}

	function subAdd(id) {
		for (let i = 0; i < todos.length; i++) {
			if (todos[i].id === id) {
				todos[i].subTodos.push({ subName: todos[i].inputSubTodo });
				todos[i].inputSubTodo = '';
			}
		}
		localStorage.setItem('todos', JSON.stringify(todos));
	}

	function addP(id) {
		for (let i = 0; i < todos.length; i++) {
			if (todos[i].id === id) {
				todos[i].numPomodoros++;
				totalNumPomodoros++;
				break;
			}
		}
		localStorage.setItem('todos', JSON.stringify(todos));
	}

	function subP(id) {
		for (let i = 0; i < todos.length; i++) {
			if (todos[i].id === id) {
				if (todos[i].numPomodoros == 0) return;
				else {
					todos[i].numPomodoros--;
					totalNumPomodoros--;
				}
				break;
			}
		}
		localStorage.setItem('todos', JSON.stringify(todos));
	}

	let prodScore = $state(0);
	let numTasksCompleted = $state(0);
	let numTasksDeleted = $state(0);
	let punctualCompleted = $state(0);
	let punctuality = $state(1);
	let timeSpentWorking = $state(0);
	let pomodorosCompleted = $state(0);
	let totalNumPomodoros = $state(0);

	for (let i = 0; i < todosNotComplete.length; i++) {
		totalNumPomodoros += todosNotComplete[i].numPomodoros;
	}

	function markComplete(id) {
		for (let i = 0; i < todos.length; i++) {
			if (todos[i].id === id) {
				todos[i].isComplete = true;
				numTasksCompleted++;
				let imp = todos[i].importance + 1;
				let subs = todos[i].subTodos.length;
				let estTime = $state(todos[i].numPomodoros * 25);
				if (todos[i].subTodos.length == 0) {
					subs = 1;
				}
				if (todos[i].numPomodoros == 0) {
					estTime = 25;
				}
				if (todos[i].dueDate > formattedToday) {
					prodScore += 4 * imp * subs * estTime;
					punctualCompleted++;
				} else if (todos[i].dueDate == formattedToday || todos[i].dueDate == '') {
					prodScore += 2 * imp * subs * estTime;
					punctualCompleted++;
				} else {
					prodScore += imp * subs * estTime;
				}
				punctuality = (punctualCompleted / numTasksCompleted).toFixed(3);
				break;
			}
		}
		localStorage.setItem('todos', JSON.stringify(todos));
	}

	function deleteTask(id) {
		if (!confirm('Are you sure you want to delete this task? This cannot be undone.')) return;
		for (let i = 0; i < todos.length; i++) {
			if (todos[i].id === id) {
				todos[i].isComplete = true;
				numTasksDeleted++;
			}
		}
		localStorage.setItem('todos', JSON.stringify(todos));
	}

	function flag(id) {
		for (let i = 0; i < todos.length; i++) {
			if (todos[i].id === id) {
				todos[i].importance = (todos[i].importance + 1) % 4;
				break;
			}
		}
		localStorage.setItem('todos', JSON.stringify(todos));
	}

	function unflag(id) {
		for (let i = 0; i < todos.length; i++) {
			if (todos[i].id === id) {
				if (todos[i].importance === '') return;
				else {
					todos[i].importance = '';
				}
				break;
			}
		}
	}

	function clearUserData() {
		if (!confirm('Are you sure you want to clear your data? This cannot be undone.')) return;
		localStorage.clear();
		window.location.reload();
	}

	const today = new Date();
	let formattedToday = today.getFullYear() + '-' + (today.getMonth() + 1) + '-0' + today.getDate();

	import ProgressBar from '../../components/ProgressBar.svelte';
	import { onMount } from 'svelte';

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
		if (workseconds <= 0) {
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
			if (workseconds <= 0) {
				resetBreakTimer();
				breakTimer();
			} else {
				workseconds--;
				workprogress += 100 / iw;
				timeSpentWorking += 1;
			}
		}, 1000);
	}

	function breakTimer() {
		clearInterval(worktimer);
		running = true;
		working = false;
		breaktimer = setInterval(() => {
			if (breakseconds <= 0) {
				resetWorkTimer();
				workTimer();
				pomodorosCompleted += 1;
			} else {
				breakseconds--;
				breakprogress += 100 / ib;
			}
		}, 1000);
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

<div
	class="faustina flex justify-center bg-gradient-to-br from-success to-green-800/50 p-3 text-6xl text-black"
>
	Task Checklist
</div>
<hr />
<div class="flex justify-between bg-gradient-to-br from-base-content via-neutral to-primary">
	<div class="flex-grow">
		<input
			class="m-2 rounded-xl border bg-secondary p-2 text-black focus:ring-2 focus:ring-primary focus:outline-none"
			placeholder="Add a new task..."
			bind:value={inputTxt}
		/>

		<input
			bind:value={inputDate}
			placeholder="Pick Due Date"
			class="m-2 rounded-xl border bg-secondary p-2 text-black focus:ring-2 focus:ring-primary focus:outline-none"
			type="date"
		/>

		<select
			class="select m-2 rounded-xl border bg-secondary p-2 text-black select-secondary focus:ring-2 focus:ring-primary focus:outline-none"
			bind:value={inputImportance}
		>
			<option value={''} disabled selected>Task Importance</option>
			<option value={1}>★</option>
			<option value={2}>★★</option>
			<option value={3}>★★★</option>
		</select>

		<select
			class="select m-2 rounded-xl border bg-secondary p-2 text-black select-secondary focus:ring-2 focus:ring-primary focus:outline-none"
			bind:value={inputCategory}
		>
			<option value={''} disabled selected>Tags</option>
			{#each categories as c}
				<option value={c}>{c}</option>
			{/each}
		</select>

		<button
			class="m-2 rounded-xl border bg-secondary p-2 font-semibold hover:bg-primary disabled:text-gray-500"
			onclick={add}
			disabled={inputTxt.length == 0}>Add Task</button
		>
	</div>
	<button
		class="m-2 rounded-xl border bg-error p-2 font-semibold text-black hover:bg-red-300 focus:ring-2 focus:ring-red-300 focus:outline-none"
		onclick={clearUserData}>Clear User Data</button
	>
</div>
<hr />

<div class="flex">
	<ul class="m-1 w-2/3 border-r p-1">
		<div class="faustina text-center text-4xl font-semibold">Tasks</div>
		<hr />
		{#each todosNotComplete as v, i}
			<li>
				<div class="font-bold {`${v.category == '' && 'hidden'}`}">[{v.category}]</div>

				<input
					type="checkbox"
					bind:checked={v.isComplete}
					class="checkbox checkbox-primary"
					onclick={() => markComplete(v.id)}
				/>
				<button
					class="m-0.5 h-8 w-16 rounded-xl border bg-accent p-1 font-semibold hover:bg-blue-300 disabled:text-gray-500"
					onclick={() => flag(v.id)}
				>
					{new Array(v.importance).fill('★').join('')}
				</button>

				{v.name} ({new Array(v.numPomodoros).fill('🍅').join('')}
				<button
					class="m-0.5 rounded-xl border bg-secondary p-1 hover:bg-primary"
					onclick={() => addP(v.id)}>+🍅</button
				>

				<button
					class="m-0.5 rounded-xl border bg-secondary p-1 hover:bg-primary"
					onclick={() => subP(v.id)}>-🍅</button
				>)

				<div class="m-0.5 pl-5">
					{#each v.subTodos as subv, subi}
						<li>
							<input type="checkbox" class="checkbox checkbox-primary" />
							{subv.subName}
						</li>
					{/each}
				</div>

				<div
					class="{`${v.dueDate == '' && 'hidden'}`} {`${v.dueDate <= formattedToday && 'font-semibold text-error'}`}"
				>
					Due: {v.dueDate}
				</div>

				<div class="text-sm">
					<button
						class="m-0.5 rounded-xl border bg-success p-1 font-semibold hover:bg-green-300 disabled:text-gray-500"
						onclick={() => startTimer(v.id)}
						disabled={running == true}>Start Timer</button
					>

					<input
						class="m-0.5 rounded-xl border bg-info p-1 text-black focus:ring-2 focus:ring-primary focus:outline-none"
						placeholder="Add a new subtask..."
						bind:value={v.inputSubTodo}
					/>

					<button
						class="m-0.5 rounded-xl border bg-info p-1 font-semibold hover:bg-purple-300 disabled:text-gray-500"
						onclick={() => subAdd(v.id)}
						disabled={v.inputSubTodo.length == 0}>Add Subtask</button
					>

					<button
						class="m-0.5 rounded-xl border bg-error p-1 font-semibold hover:bg-red-300"
						onclick={() => deleteTask(v.id)}>Delete Task</button
					>
				</div>

				<div
					class={`${activeTask !== v.id && 'hidden'} card m-3 w-120 border border-2 border-accent bg-secondary text-primary-content`}
				>
					<div class="card-body">
						<div class="card-actions justify-end">
							<button
								class="btn btn-square border border-accent bg-primary btn-sm"
								onclick={hidePopup}
							>
								<svg
									xmlns="http://www.w3.org/2000/svg"
									class="h-6 w-6"
									fill="none"
									viewBox="0 0 24 24"
									stroke="currentColor"
								>
									<path
										stroke-linecap="round"
										stroke-linejoin="round"
										stroke-width="2"
										d="M6 18L18 6M6 6l12 12"
									/>
								</svg>
							</button>
						</div>

						<div class={`${working !== true && 'hidden'}`}>
							<h2 class="card-title">Task Started!</h2>
							<div>
								You have {parseInt(workseconds / 60)} minutes and {workseconds % 60} seconds left until
								your next break.
							</div>

							<ProgressBar color={'bg-error'} current={workprogress} max={100} />
						</div>

						<div class={`${working === true && 'hidden'}`}>
							<h2 class="card-title">Break Time!</h2>
							<div>
								You have {parseInt(breakseconds / 60)} minutes and {breakseconds % 60} seconds left until
								your next work session. Try to avoid checking social media.
							</div>

							<ProgressBar color={'bg-success'} current={breakprogress} max={100} />
						</div>

						<div class="card-actions justify-end">
							<button
								class="m-0.5 rounded-xl border bg-warning p-1 font-semibold hover:bg-yellow-300"
								onclick={stopTimer}>Stop Timer</button
							>
							<button
								class="m-0.5 rounded-xl border bg-error p-1 font-semibold hover:bg-red-300"
								onclick={resetTimer}>Reset Timer</button
							>
						</div>
					</div>
				</div>
				<hr class="mt-2 mb-2" />
			</li>
		{/each}
	</ul>
	<div class="m-1 w-1/3 border-l p-1">
		<div class="faustina text-center text-4xl font-semibold">Statistics</div>
		<hr />
		<div class="flex flex-wrap justify-between text-sm font-semibold">
			<div
				class=" tooltip m-1 h-12 rounded-xl border-3 border-purple-300 bg-info p-2 font-semibold"
				data-tip="Calculated based on the importance, number of pomodoros, number of subtasks, and completion date of all tasks"
			>
				Productivity Score: {prodScore}
			</div>
			<div class="m-1 flex h-12 rounded-xl border-3 border-accent bg-secondary p-2">
				Current Tasks: {todosNotComplete.length}
			</div>
			<div class="m-1 flex h-12 rounded-xl border-3 border-accent bg-secondary p-2">
				Tasks Completed: {numTasksCompleted}
			</div>
			<div class="m-1 flex h-12 rounded-xl border-3 border-accent bg-secondary p-2">
				Tasks Deleted: {numTasksDeleted}
			</div>
			<div class="m-1 h-12 rounded-xl border-3 border-red-300 bg-error p-2 font-semibold">
				Pomodoros Completed: {pomodorosCompleted}
			</div>

			<div
				class="tooltip m-1 h-12 rounded-xl border-3 border-red-300 bg-error p-2 font-semibold"
				data-tip="Based on your usage of the built-in pomodoro timer"
			>
				Time Spent Working: {parseInt(timeSpentWorking / 3600)} hours, {parseInt(
					timeSpentWorking / 60
				) % 60} minutes, and {timeSpentWorking % 60} seconds
			</div>
			<div
				class="tooltip m-1 h-12 rounded-xl border-3 border-red-300 bg-error p-2 font-semibold"
				data-tip="The total number of pomodoros across all of your tasks, multiplied by 30"
			>
				Estimated Time Until All Remaining Tasks are Compelete: {totalNumPomodoros * 30} minutes
			</div>
			<div
				class="tooltip m-1 h-12 rounded-xl border-3 border-green-300 bg-success p-2 font-semibold"
				data-tip="The number of tasks you have done on or ahead of time divided by the total number of tasks you have completed"
			>
				Punctuality: {punctuality * 100}%
			</div>
		</div>
	</div>
</div>
