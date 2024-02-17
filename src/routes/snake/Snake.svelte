<script lang="ts">
    interface Props {
        width: number;
    }
    
    const {
      width,
    } = $props<Props>();

    interface Cell {
        on: boolean;
    }

    let grid : Cell[][] = $derived(Array<Cell[]>(width).fill(Array<Cell>(width).fill({ on: false })));

    // $inspect(grid);
</script>

<h1>Snake</h1>

{#each grid as row}
    <div class="row">
        {#each row as cell}
            <div class="cell" style="--bg-color: {cell.on ? "#000" : "#FFF"}"></div>
        {/each}
    </div>
{/each}

<style>
    .row {
        display: flex;
    }
    .cell {
        display: flex;
        min-width: 1rem;
        min-height: 1rem;
        background-color: var(--bg-color);
        border: 1px solid rgb(0 0 0 / 5%)
    }
</style>