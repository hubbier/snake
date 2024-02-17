<script lang="ts">    
  const {
    width,
    setWidth
  } = $props();
</script>

<h1>Settings</h1>
<input type="range" id="width" name="width" min="2" max="20" value={width} onchange={setWidth} step="1" />
<label for="width">Width ({width})</label>