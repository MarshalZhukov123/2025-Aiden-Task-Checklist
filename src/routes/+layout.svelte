<script>
	import '../app.css';

	/** @type {{children: import('svelte').Snippet}} */
	let { children } = $props();
</script>

<div class="flex min-h-screen flex-col">
	<main class="flex-grow bg-gradient-to-b from-secondary via-base-300 to-base-200">
		{@render children()}
	</main>
	<div>
		<hr />

		<div class="flex justify-around bg-gradient-to-br from-base-content via-neutral to-primary">
			<a class="btn m-2 rounded-xl border-accent btn-secondary" href="/">Home</a>
			<a class="btn m-2 rounded-xl border-accent btn-secondary hover:btn-error" href="/pomodoro"
				>Pomodoro</a
			>
			<a class="btn m-2 rounded-xl border-accent btn-secondary hover:btn-warning" href="/tasks"
				>Task Checklist</a
			>
			<a class="btn m-2 rounded-xl border-accent btn-secondary hover:btn-success" href="/habits"
				>Habit Builder</a
			>
			<a class="btn m-2 rounded-xl border-accent btn-secondary hover:bg-yellow-400" href="/tags"
				>Tags</a
			>
			<a class="btn m-2 rounded-xl border-accent btn-secondary hover:bg-yellow-400" href="/stats"
				>All-Time Stats</a
			>
		</div>

		<hr />
	</div>
</div>
