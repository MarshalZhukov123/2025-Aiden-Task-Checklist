<script>
	let tags = $state([
		{ name: 'Tag 1', deleted: false, id: 1, creationDate: '' },
		{ name: 'Tag 2', deleted: false, id: 2, creationDate: '' },
		{ name: 'Tag 3', deleted: false, id: 3, creationDate: '' }
	]);
	let kepttags = $derived(tags.filter((tag) => tag.deleted == false));
	let inputTag = $state('');
	const today = new Date();
	const formattedToday = today.getFullYear() + '-' + (today.getMonth() + 1) + '-' + today.getDate();
	function addTag() {
		tags.push({ name: inputTag, deleted: false, id: Date.now(), creationDate: Date.now() });
		inputTag = '';
	}
	function deleteTag(id) {
		// if (!confirm('Are you sure?')) return;
		for (let i = 0; i < tags.length; i++) {
			if (tags[i].id === id) {
				tags[i].deleted = true;
			}
		}
	}
	let sortKey = $state('az');

	let sortedList1 = $derived([...tags].sort((a, b) => a.name.localeCompare(b.name)));

	let sortedList2 = $derived(
		[...tags].sort((b, a) => a.creationDate.localeCompare(b.creationDate))
	);

	let sortedList3 = $derived([...tags].sort((a) => a.creationDate.localeCompare(b.creationDate)));

	let sortedList = $derived(
		sortKey == 'az' ? sortedList1 : sortKey == 'date' ? sortedList2 : sortedList3
	);

	let filteredSorted = $derived(sortedList.filter((tag) => tag.deleted == false));
</script>

<div
	class="faustina flex justify-center bg-gradient-to-br from-info to-purple-900/50 p-3 text-6xl text-black"
>
	Tags
</div>
<hr />
<div class="bg-gradient-to-br from-base-content via-neutral to-primary">
	<input
		class="m-2 rounded-xl border bg-purple-300/50 p-2 text-black focus:ring-2 focus:ring-primary focus:outline-none"
		placeholder="Add a new tag..."
		bind:value={inputTag}
	/>

	<button
		class="m-2 rounded-xl border bg-purple-300/50 p-2 font-semibold hover:bg-purple-200/70 disabled:text-gray-500"
		onclick={addTag}
		disabled={inputTag.length == 0}>Add Tag</button
	>
	<select
		bind:value={sortKey}
		class="select m-2 rounded-xl border bg-purple-300/50 p-2 text-black select-secondary focus:ring-2 focus:ring-primary focus:outline-none"
	>
		<option value={''} disabled selected>Sort Tags</option>
		<option value={'az'}>Alphabetically (A-Z)</option>
		<option value={'date'}>Date Created (newest)</option>
		<option value={'date2'}>Date Created (oldest)</option>
	</select>
</div>
<hr />
<div class="flex flex-wrap">
	{#each filteredSorted as tag}
		<div class="m-0.5 p-1">
			<div class=" m-0.5 rounded-xl border bg-info p-1 font-semibold">
				{tag.name}
				<button
					class="m-1 rounded-xl border bg-error p-1 text-sm font-semibold hover:bg-red-300"
					onclick={() => deleteTag(tag.id)}>❌</button
				>
			</div>
		</div>
	{/each}
</div>
