<script>
	
	import '../app.css';

	/** @type {{children: import('svelte').Snippet}} */
	let { children } = $props();
</script>

<div class="min-h-screen flex flex-col">

	<main class="bg-gradient-to-b from-secondary via-base-300 to-base-200 flex-grow">
		{@render children()}
	</main>
	<div>

	<hr/>

	<div class="bg-gradient-to-br from-base-content via-neutral to-primary flex justify-around">
		<a class="btn btn-secondary m-2 border-accent rounded-xl" href="/">Home</a>
		<a class="btn btn-secondary m-2 border-accent rounded-xl hover:btn-error" href="/pomodoro">Pomodoro</a>
		<a class="btn btn-secondary m-2 border-accent rounded-xl hover:btn-warning" href="/tasks">Task Checklist</a>
		<a class="btn btn-secondary m-2 border-accent rounded-xl hover:btn-success" href="/habits">Habit Builder</a>
		<a class="btn btn-secondary border-accent rounded-xl m-2 hover:bg-yellow-400" href="/stats">All-Time Stats</a>
	</div>

	<hr/>
	
	</div>
</div>
