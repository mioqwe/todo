<script>
    import { notebooks } from "$lib/store"
    console.log("u are in settings")
</script>

{#if $notebooks.length}
    <div class="container mx-auto flex flex-col gap-3 mt-3">
		{#each $notebooks as notebook, index}
			{#if notebook.name}
				<button on:click={() => {
                        console.log(index)
                        $notebooks.splice(index, 1)
                        $notebooks = $notebooks
                    }} 
                    class="py-2 px-2 mx-2 rounded border-rose-500 border flex justify-between">
                    <span>
                        {notebook.name}
                    </span>
                    <span>
                        <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" fill="currentColor" class="bi bi-x-square-fill" viewBox="0 0 16 16">
                        <path d="M2 0a2 2 0 0 0-2 2v12a2 2 0 0 0 2 2h12a2 2 0 0 0 2-2V2a2 2 0 0 0-2-2zm3.354 4.646L8 7.293l2.646-2.647a.5.5 0 0 1 .708.708L8.707 8l2.647 2.646a.5.5 0 0 1-.708.708L8 8.707l-2.646 2.647a.5.5 0 0 1-.708-.708L7.293 8 4.646 5.354a.5.5 0 1 1 .708-.708"/>
                        </svg>
                    </span>
                </button>	
			{/if}
		{/each}
    </div>
{/if}