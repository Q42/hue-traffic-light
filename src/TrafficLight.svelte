<script>
    export let ip;
    export let token;
    export let lightIndex;
    const api = `https://${ip}/api/${token}`
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

    function chooseLight() {
        localStorage.removeItem('light');
        document.location.reload();
    }
    turnOff();
    updateTrafficLight();
</script>

<main>
    <h1>What is your <br> Traffic Light Color?</h1>
    <div class="wrapper">
        <div class="traffic-light">
            <div class="light red" style="opacity: { opacityRed }" on:click="{clickRed}" ></div>
            <div class="light orange" style="opacity: { opacityOrange }" on:click="{clickOrange}" ></div>
            <div class="light green" style="opacity: { opacityGreen }" on:click="{clickGreen}"></div>
        </div>
    </div>

    <button on:click="{chooseLight}">choose light</button>
    <button on:click="{reset}">reset</button>
    
    <!-- <div class="debug">
        using ip: {ip} <br>
        using token: {token}
    </div> -->
</main>

<style>
    
    h1 {
        text-align: center;
        color: #cbcbcb;
    }

    .wrapper {
        position: relative;
        flex-grow: 1;
    }
    .traffic-light {
        background-color: #242424;
        border-radius: 40%/16%;
        border: 5px solid grey;
        margin-bottom: 20px;
        height: 350px;
        width: 100px;
        padding: 30px;
        display: flex;
        flex-direction: column;
        justify-content: space-evenly;
    }

    .light {
        width: 90%;
        height: 0;
        padding-top: 90%;
        border-radius: 50%;
        transition: opacity 0.2s linear;
        align-self: center;
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

    .debug {
        color: #303030;
    }
    
</style>
