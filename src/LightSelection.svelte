<script>
    export let lightIndex;
    export let ip;
    export let token;
    
    let lights = [];

    async function getLights() {
        const url = `https://${ip}/api/${token}/lights`;
        const response = await fetch(url);
        const json = await response.json();
        
        console.log(json);
        if (json) {
            lights =  Object.values(json);
        }
    }

    function select(number) {
        lightIndex = number+1;
        console.log('setting lightIndex to', lightIndex)
        localStorage.setItem('light', lightIndex)
        document.location.reload();
    }
    getLights();
</script>


<main>
    <h1>
        Which light do you want to use for the Traffic light?
    </h1>
    
    {#each lights as {name}, i }
        <button on:click={e => select(i) } >{name}</button>
    {/each}
    
</main>

<style>
    h1 {
        flex-grow: 1;;
    }
</style>
