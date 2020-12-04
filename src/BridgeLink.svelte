<script>
import App from "./App.svelte";
    export let ip;
    export let token;
    let tokenError = null;

    const app_id = 'TRAFFIC_LIGHT';
    const device_id = 123; // TODO random generate;
    const discoverUrl = 'https://discovery.meethue.com/';

    async function discoverBridge() {
        const response = await fetch(discoverUrl);
        const json = await response.json();
        console.log(json);
        if (json.length && json[0] && json[0]['internalipaddress'] ) {
            ip = json[0].internalipaddress;
        }
    }

    async function requestToken() {
        const url = `http://${ip}/api`
        const response = await fetch(url, {
            method: 'POST',
            body: JSON.stringify({ devicetype: `${app_id}#${device_id}`})
        });
        const json = await response.json();
        console.log(json);
        if (json && json.length && json[0]) {
            if (json[0].error) {
                tokenError = json[0].error;
            }
            if (json[0].success) {
                tokenError = null;
                token = json[0].success.username;

                // TODO: store ip && token in localstorage
            }
            
        }
    }
    function cancel() {
        ip = null;
    }
</script>

{#if !ip}
    <div >Looking for bridge</div>
    <button on:click="{discoverBridge}">discover bridge</button>
{:else}
    <div>Bridge found {ip}</div>
    {#if !token}
        <div>Token needed</div>
        {#if tokenError}
            {tokenError.description}
        {/if}
        <button on:click="{requestToken}">request token</button>
    {/if}
{/if}

{#if ip || token}
    <button on:click="{cancel}">cancel</button>
{/if}


