<script>
    import BridgeLink from './BridgeLink.svelte';
    import TrafficLight from './TrafficLight.svelte';
    let ip;
    let token;
</script>

<main>
    {#if ip && token}
        <TrafficLight ip="{ip}" token="{token}" />
    {:else}
        <BridgeLink bind:ip="{ip}" bind:token="{token}" /> 
    {/if}
</main>