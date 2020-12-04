<script>
    export let ip;
    export let token;
    const api = `http://${ip}/api/${token}`
    const lightIndex = 2;
    const colors = { red: 0, orange: 25, green: 80 };
    const colorNames = Object.keys(colors);
    const trafficLight = [false, false, false]; // red, green, blue

    let opacityRed = 0;
    let opacityOrange = 0;
    let opacityGreen = 0;

    function clickRed() {
        clickLight('red');
    }

    function clickOrange() {
        clickLight('orange');
    }

    function clickGreen() {
        clickLight('green');
    }

    async function clickLight(color) {
        // TODO: temp disable clicking
        const newColorIndex = colorNames.indexOf(color);
        
        // check if a color was already on
        const currentColorIndex = trafficLight.indexOf(true);
        if (currentColorIndex >= 0 && currentColorIndex == newColorIndex) {
            await turnOff();
        }
        else {
            await setColor(color);
            trafficLight[newColorIndex] = true;
        }
        trafficLight[currentColorIndex] = false;
        updateTrafficLight();
    }  

    async function turnOff() {
        setState(lightIndex, { on: false });
    }

    async function setColor(color) {
        const hue = getHue(color) * 256;
        setState(lightIndex, { on:true, sat:254, bri:254, hue: hue } );
    }

    async function setState(number, cmd) {
        const url = `${api}/lights/${number}/state`;
        
        const response = await fetch(url, {
            method: 'PUT',
            body: JSON.stringify(cmd)
        })
        const json = await response.json();
        
        json.forEach(element => {
            if ('error' in element) {
                console.error('error executing', cmd)
                console.error(element.error);
            }
        });
    }

    function getHue(color)  {
        const hue = colors[color];
        return hue;
    }
    
    function updateTrafficLight() {
        opacityRed = trafficLight[0] * 1 + 0.2;
        opacityOrange = trafficLight[1] * 1 + 0.2;
        opacityGreen = trafficLight[2] * 1 + 0.2;
    }

    function reset() {
        localStorage.clear();
        document.location.reload();
    }

    turnOff();
    updateTrafficLight();
</script>

<main>
    <h1>What is your <br> Traffic Light Color?</h1>
    <div class="traffic-light">
        <div class="light red" style="opacity: { opacityRed }" on:click="{clickRed}" ></div>
        <div class="light orange" style="opacity: { opacityOrange }" on:click="{clickOrange}" ></div>
        <div class="light green" style="opacity: { opacityGreen }" on:click="{clickGreen}"></div>
    </div>

    <!-- <div>
        using ip: {ip}
        using token: {token}
    </div> -->
    <button on:click="{reset}">reset</button>
</main>

<svelte:head>
    <style>
        body {
            background-color: #555555;
        }
    </style>
</svelte:head>

<style>
    
    h1 {
        text-align: center;
        color: #9f9f9f;
    }
    main {
        font-family:'Trebuchet MS', 'Lucida Sans Unicode', 'Lucida Grande', 'Lucida Sans', Arial, sans-serif;
        display:flex;
        flex-direction: column;
        justify-content: space-around;
    }
    .traffic-light {
        background-color: #242424;
        padding: 20px;
        border-radius: 50px;
        margin: auto;
        flex-shrink: 1;
    }
    .light {
        width: 100px;
        height: 100px;
        border-radius: 50%;
        margin-bottom: 10px;
        transition: opacity 0.2s linear;
    }
    .red {
        background-color: red;
    }
    .orange {
        background-color: orange;
    }
    .green {
        background-color: lime;
        margin: 0;
    }
</style>
