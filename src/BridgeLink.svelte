<script>
    export let ip;
    export let token;
    const device_id = 42; 
    const app_id = 'TRAFFIC_LIGHT';
    const discoverUrl = 'https://discovery.meethue.com/';
    let tokenError = null;
    let bridgeError = null;
    let pollTimout = null;

    async function discoverBridge() {
        const response = await fetch(discoverUrl);
        const json = await response.json();
        // console.log(json);
        if (json.length && json[0] && json[0]['internalipaddress'] ) {
            return json[0].internalipaddress;
        }
        throw 'no bridge found';
    }

    async function requestToken() {
        const url = `https://${ip}/api`;
        const response = await fetch(url, {
            method: 'POST',
            body: JSON.stringify({ devicetype: `${app_id}#${device_id}`})
        }).catch(e=> bridgeError = e.message);
        
        const json = await response.json();
        
        // console.log(json)
        if (json && json.length && json[0]) {
            if (json[0].error) {
                tokenError = json[0].error;
            }
            if (json[0].success) {
                tokenError = null;
                token = json[0].success.username;
                clearTimeout(pollTimout);

                localStorage.setItem('ip', ip);
                localStorage.setItem('token', token);
            }
            
        }
    }

    async function pollBridgeForButtonPress() {
        console.log('polling');
        try {
            await requestToken();
        }
        catch(ex) {
            console.error('oh jee', ex);
            return;
        }
        if (!token)
            pollTimout = setTimeout(pollBridgeForButtonPress, 500);
    }

    async function scan() {
        ip = await discoverBridge()
        pollBridgeForButtonPress();
    }

    function cancel() {
        ip = null;
    }
</script>

<main>
    
    {#if !ip}
        <h1>Connecting your bridge</h1>
        
        <button on:click="{scan}">scan</button>
        
    {:else}
        {#if !token}
        {#if tokenError}
        <!-- {tokenError.description} -->
        <h1>Press the button on your bridge</h1>
        {/if}
        <!-- <div>Token needed</div> -->
        <!-- <button on:click="{requestToken}">request token</button> -->
        {/if}
        <p>Bridge found {ip}</p>
        {#if bridgeError}
            <p>{bridgeError}</p>
            <p>This is probably due to the self-signed certificate of your bridge.</p>
            <p>You need to <a href="http://www.google.com/search?q=trust+self-signed+certificate" target="_blank">trust the self-signed certificate</a>.</p>
        {/if}
    {/if}

    {#if ip || token}
        <button on:click="{cancel}">cancel</button>
    {/if}
    
</main>

<style>
    h1 {
        flex-grow: 1;;
    }
</style>
