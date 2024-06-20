<script>
	import { TabGroup, Tab, TabAnchor } from '@skeletonlabs/skeleton';
	import { notebooks } from '$lib/store'
	import { get } from 'svelte/store';
	import { onMount } from 'svelte';
	import { initializeStores, Modal } from '@skeletonlabs/skeleton';
	import { getModalStore } from '@skeletonlabs/skeleton';
	initializeStores();
	const modalStore = getModalStore();

	let tabClicked = {id: 0, times:0, name: undefined} 
	let notebookName
	let taskStatuses = ["created",  "progress", "done"]

	const modal = {
		type: 'prompt',
		title: '- todo',
		body: 'Enter task:',
		value: '',
		valueAttr: { type: 'text', minlength: 1, required: true, placeholder: "window jump" },
		response: (response) => {
			console.log(tabIndex)
			const task = {name: response, status: "created"}
			console.log(task)
			const foundNotebook = $notebooks.find(notebook => notebook.id === tabIndex);
			if (foundNotebook && foundNotebook.tasks){
				foundNotebook.tasks.push(task) 
				$notebooks = $notebooks 
			}
			console.log(response)
		} 
	};


	function tabClick(notebook) {
		// let clickedName = notebook.name; // Declare a variable to store the clicked name
		// console.log(notebook.name)
		// console.log(tabClicked)
  		let nameSame = false;
		tabIndex = notebook.id
		console.log(tabIndex)

		if (tabClicked.name === notebook.name) { // Use the stored clickedName variable
    		nameSame = true;
  		} else {
			tabClicked = tabClicked
			tabClicked.name = notebook.name 
  		}

		tabClicked.times += 1
		tabClicked = tabClicked

		if (tabClicked.times === 2 && nameSame === true) {
			modalStore.trigger(modal);
			tabClicked.times = 1 
			tabClicked = tabClicked
		} else {
			tabClicked.times = 1
			tabClicked = tabClicked
		}

	}
	onMount(async () => {
		if (Array.isArray($notebooks)) {
  			console.log("$notebooks is a true JavaScript array.");
		} else {
			$notebooks = []
  			console.log("$notebooks is not a true JavaScript array.");
		}
	})
	let tabIndex = 0 
	
</script>

<Modal zIndex={'z-999'}/>

<div class="container mx-auto">
	<div class="mt-3 mx-2">
		<form on:submit={(e) => {
				console.log(e.target)
				const form = e.target
				let nextIndex = 0
				if (Array.isArray($notebooks) && $notebooks.length > 0) {
  					nextIndex = $notebooks.reduce((maxId, notebook) => Math.max(maxId, notebook.id), 0) + 1;
				} else {
  					console.warn("Notebooks list is empty or not an array. Using default nextIndex = 0.");
				}
				let noteName = form.elements.noteName.value
				const newNotebook = {
					name: noteName, 
					id: nextIndex,
					tasks: [] 
				}
				$notebooks = [...$notebooks, newNotebook];
				console.log($notebooks)
				notebookName = ""

				}}>
			<label class="label">
				<input class="input" bind:value={notebookName} name="noteName" type="text" placeholder="NoteBook" />
			</label>
		</form>
	</div>

	<TabGroup class="mt-2">
		{#if $notebooks.length}
			{#each $notebooks as notebook, index}
				{#if notebook.name}
					<Tab on:click={() => tabClick(notebook)} bind:group={tabIndex} name={notebook.name} value={notebook.id}>
						<span>{notebook.name}</span>
					</Tab>
				{/if}
			{/each}
		{/if}

		<svelte:fragment slot="panel">

			{#if $notebooks.length && $notebooks.find(notebook => notebook.id === tabIndex)}
			<div class="flex flex-col gap-3">
				{#each $notebooks.find(notebook => notebook.id === tabIndex).tasks as task}
					{#if task.name}
						<button 
						on:click={() => {
							task.status = taskStatuses.find(e => e == task.status)
							const index = taskStatuses.indexOf(task.status);
							if (index !== -1 && index < taskStatuses.length - 1) {
								// Update the next item
								task.status = taskStatuses[index + 1];
							} else {
								task.status = taskStatuses[0]
							}
							console.log(task.status)
						}}
						class:border-emerald-500={task.status == "done"}
						class:border-orange-500={task.status == "progress"}
						class:border-gray-500={task.status == "created"} class="py-2 px-2 text-left mx-2 border-2 rounded-md">{task.name}</button>	

					{/if}
						
				{/each}
			</div>
			{/if}
		</svelte:fragment>
	</TabGroup>

</div>