<script>
    import { createEventDispatcher, onMount } from "svelte";
    import Chip from "$lib/components/Chip.svelte";

    export let style = "";
    export let items = [];
    export let selectedIds = [];
    export let multiple = true;
    export let optional = true;
    export let defaultId = null;

    const dispatch = createEventDispatcher();

    // Set the default selected chip on component mount
    onMount(() => {
        if (defaultId && !selectedIds.includes(defaultId)) {
            if (selectedIds == []) {
                selectedIds = [defaultId];
            }
            dispatch("selectionChange", { selectedIds });
        }
    });

    function handleChipChange(id, checked) {
        let updatedSelectedIds;
        if (multiple) {
            // Multiple selection allowed
            if (checked) {
                updatedSelectedIds = [...selectedIds, id];
            } else {
                // If optional is false and it's the last selected chip, prevent deselecting
                if (!optional && selectedIds.length === 1) return;
                updatedSelectedIds = selectedIds.filter((item) => item !== id);
            }
        } else {
            // Only one selection allowed
            if (checked) {
                updatedSelectedIds = [id]; // Deselect all others and select the new one
            } else {
                if (optional) {
                    updatedSelectedIds = [];
                } else {
                    updatedSelectedIds = [id]; // Prevent deselection if optional is false
                }
            }
        }

        // Update selectedIds and dispatch event
        selectedIds = updatedSelectedIds;
        dispatch("selectionChange", { selectedIds });
    }
</script>

<div {style} class="chip-group">
    {#each items as item (item.id)}
        <Chip
            checked={selectedIds.includes(item.id)}
            on:change={(event) =>
                handleChipChange(item.id, event.detail.checked)}
        >
            {item.label}
        </Chip>
    {/each}
</div>

<style>
    .chip-group {
        display: flex;
        flex-wrap: wrap;
        gap: 0.4em;
        margin: 0.4em;
        flex-direction: column;
    }
</style>
