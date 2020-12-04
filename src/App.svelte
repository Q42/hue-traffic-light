<script>
    import BridgeLink from './BridgeLink.svelte';
    import TrafficLight from './TrafficLight.svelte';

    let ip = localStorage.getItem('ip');
    let token = localStorage.getItem('token');
</script>


{#if ip && token}
    <TrafficLight ip="{ip}" token="{token}" />
{:else}
    <BridgeLink bind:ip="{ip}" bind:token="{token}" /> 
{/if}
