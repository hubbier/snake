<script lang="ts">
	import Settings from "./Settings.svelte";
	import Snake from "./Snake.svelte";

    let width = $state(20);
    function setWidth(e) {
        width = parseInt(e.target.value);
    }
</script>

<div>
    <section class="left">
        <Settings width={width} setWidth={setWidth} />
    </section>
    <section class="right">
        <Snake width={width} />
    </section>
</div>

<style>
    div {
        display: flex;
    }
    .left {
        width: 20rem;
    }
</style>