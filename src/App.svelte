<script>
    import BridgeLink from './BridgeLink.svelte';
    import TrafficLight from './TrafficLight.svelte';
    import LightSelection from './LightSelection.svelte';

    let ip = localStorage.getItem('ip');
    let token = localStorage.getItem('token');
    let lightIndex = localStorage.getItem('light');
    console.log(ip,token,lightIndex)
</script>


{#if ip && token && lightIndex}
    <TrafficLight ip="{ip}" token="{token}" lightIndex="{lightIndex}" />
{:else if ip && token}
    <LightSelection bind:light="{lightIndex}" ip="{ip}" token="{token}" />
{:else}
    <BridgeLink bind:ip="{ip}" bind:token="{token}" /> 
{/if}
