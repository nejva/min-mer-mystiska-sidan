<script>

    let hunger = 100
    let cleanliness = 100
    let energy = 80
    let wakefullness = 100
    let bonding = 1

    let action_icon = ""
    let display = "none"
    let bowl_display = "inline"
    let soap_display = "none"
    let leash_display = "none"
    let lamp_display = "none"
    let scroll_direction = ""
    let scroll_anim = false
    let brightness = 1

    let bathroom = "Bathroom.png"
    let standard = "StandardRoom.png"
    let garden = "Backyard2.png"
    let bedroom = "Bedroom.png"
    let sidewalk = "Sidewalk.png"
    let big_background = "Cloudedsky.png"

    let petname = "🐾 Rename..."
    let skin = "Goldie_v02.png"
    let background = standard

    let sit = true
    let walk = false
    let sleep = false

    // Tar användarens input och sparar det som nytt namn.
    let new_name;
    function Rename(){
        new_name = petname
    }

    // Ljudeffekter
    let interact_audio;
    function audioInteract(){
        interact_audio.currentTime = 0;
        interact_audio.play();
    }
    let levelup_audio;
    function audioLevelUp(){
        levelup_audio.currentTime = 0;
        levelup_audio.play();
    }
    let lamp_audio;
    function audioLamp(){
        lamp_audio.currentTime = 0;
        lamp_audio.play();
    }
    let soap_audio;
    function audioSoap(){
        soap_audio.currentTime = 0;
        soap_audio.play();
    }

    // Byter rum och visar den action-knapp som passar till rummet.
    function Clean(){
        background = bathroom
        big_background = "Cloudedsky.png"
        soap_display = "inline"
        bowl_display = "none"
        leash_display = "none"
        lamp_display = "none"
        scroll_anim = false

        walk = false
        sleep = false
        sit = false
    } function Feed(){
        background = standard
        big_background = "Cloudedsky.png"
        soap_display = "none"
        bowl_display = "inline"
        leash_display = "none"
        lamp_display = "none"
        scroll_anim = false

        walk = false
        sleep = false
        sit = true
    } function Sleep(){
        background = bedroom
        big_background = "Cloudedsky.png"
        soap_display = "none"
        bowl_display = "none"
        leash_display = "none"
        lamp_display = "inline"
        brightness = 1
        scroll_anim = false

        walk = false
        sit = false
        sleep = false
    } function Backyard(){
        background = garden
        big_background = "Cloudedsky.png"
        soap_display = "none"
        bowl_display = "none"
        lamp_display = "none"
        leash_display = "inline"
        scroll_anim = false

        walk = false
        sleep = false
        sit = false
    }

    // Boostar mood som hör till respektive rum/action samt triggar ev. övriga specialattribut.
    function Action(){
        if (background===bathroom){
            cleanliness += 20
        } 
        else if(background===standard){
            bowl_display = "inline"
            hunger += 20
        } 
        else if(background===garden){
            background = sidewalk
            leash_display = "none"
            scroll_anim = true
            scroll_direction = "repeat-x"
            walk = true

            energy += 60
            wakefullness -= 20
        } 
        else if(background===bedroom){
            /*
            Snoring
            Timer
            */
            sleep = true
            sit = true
            background = "Bedroom-night.png"
            big_background = "Cloudedsky-night.png"
            brightness = 0.5

            energy += 50
            wakefullness = 100
        }
    }

    // https://www.geeksforgeeks.org/javascript/how-to-select-a-random-element-from-array-in-javascript/
    let all_moods = ["hunger","cleanliness","energy","wakefullness"];
    const getRandomIndex = (arr) => Math.floor(Math.random() * arr.length);

    // Använder det slumpade indexet ur listan(se r.92-93) för att välja ett statusvärde att sänka.
    function drainMood(){
        let mood = all_moods[getRandomIndex(all_moods)];

        const min = 2000
        const max = 10000
        let r_time = Math.floor(Math.random() * (max - min + 1)) + min;
        
        setTimeout(()=>{
            if (mood === "hunger" && hunger > 0)
                hunger -= 15;
            if (mood === "cleanliness" && cleanliness > 0)
                cleanliness -= 12;
            if (mood === "energy" && energy > 0)
                energy -= 10;
            if (mood === "wakefullness" && wakefullness > 0)
                wakefullness -= 8;

            drainMood();
        }, r_time);
    }
    drainMood()
    
    /* 
    Belönar spelaren med bonding-poäng om den lyckas hålla djurets genomsnittliga "mood" över hälften under en period.
    Blir djuret olyckligt ges inga fler bonding-poäng ut tills spelaren tagit hand om djuret.

    Är djurets "mood"-genomsnitt högre än hälften kommer intervallen ge poäng efter tiden gått, sedan starta om. Sjunker genomsnittet clearas intervallet.
    */
    let interval;
    function BondingTimer(){
        let mood_tot = (hunger + cleanliness + energy + wakefullness) / 4;

        if (mood_tot>50 && !interval){
            interval = setInterval(()=>{ 
                bonding += 1
                // audioLevelUp() <-- Spelet blir för segt
            }, 20000);
        }else if(mood_tot<50 && interval){
            clearInterval(interval)
            interval = undefined
        }
    }
    setInterval(()=>BondingTimer(),1000)

</script>

<main class="background scroll_back" style="background-image: url({big_background});">
    <div class="container">
        <form on:submit|preventDefault={Rename}>
            <input class="namecontainer" type="text"  id="petname" minlength="2" maxlength="35" placeholder={petname} bind:value={petname}/>
            <img id="heart" src="Heart.png" alt="">
            <label for="heart" id="level">{bonding}x</label>
        </form> 
        <section class="inside_container">
            <div class="pet-container" style="background-image: url({background});" class:scroll_back={scroll_anim}>
                <div class="petspace">
                    <label for="pet" id="nameplate">{new_name}🐾</label>
                    <div id="pet">
                        <img class="spritesheet pixelart" class:sitting={sit} class:walking={walk} class:sleeping={sleep} src={skin} alt="Pet">
                    </div>
                    <img class="action pixelart" src="BowlFull.png" alt="" style="display:{bowl_display};" on:click={()=>Action()} on:click={()=>audioInteract()}>
                    <img class="action pixelart" src="Soap2.png" alt="" style="display:{soap_display};" on:click={()=>Action()} on:click={()=>audioSoap()}>
                    <img class="action pixelart" src="Leash.png" alt="" style="display:{leash_display};" on:click={()=>Action()} on:click={()=>audioInteract()}>
                    <img class="action pixelart" id="lamp" src="Lamp.png" alt="" style="display:{lamp_display}; filter: brightness({brightness})" on:click={()=>Action()} on:click={()=>audioLamp()}>
                    
                    <audio bind:this={interact_audio} src="spinopel-blow-to-a-fragile-object-456376.mp3"></audio>
                    <audio bind:this={lamp_audio} src="freesound_community-desk-lamp-switch-101351.mp3"></audio>
                    <audio bind:this={soap_audio} src="freesound_community-soap-bubbles-pop-96873.mp3"></audio>
                    <audio bind:this={levelup_audio} src="floraphonic-cute-level-up-3-189853.mp3"></audio>
                </div>
            </div>

            <div class="stats">
                <div>
                    <button class="interact" on:click={()=>Feed()} on:click={()=>audioInteract()}>🍽️</button>
                    <progress id="hunger" value="{hunger}" max="100" min="0"></progress>
                </div>
                <div>
                    <button class="interact" on:click={()=>Clean()} on:click={()=>audioInteract()}>🛁</button>
                    <progress id="cleanliness" value="{cleanliness}" max="100" min="0"></progress>
                </div>
                <div>
                    <button class="interact" on:click={()=>Backyard()} on:click={()=>audioInteract()}>🥎</button>
                    <progress id="energy" value="{energy}" max="100" min="0"></progress>
                </div>
                <div>
                    <button class="interact" on:click={()=>Sleep()} on:click={()=>audioInteract()}>🌘</button>
                    <progress id="wakefullness" value="{wakefullness}" max="100" min="0"></progress>
                </div>
            </div>
        </section>
    </div>
</main>

<style>
    .inside_container{
        display: grid;
        grid-template-columns: 1fr 1fr;
        width: 100%;
        position: relative;
        gap:1em;
    }
    img{
        justify-self: center;
    }
    button{
        height:50px;
        width:50px;
        border-radius: 50px;
        border: solid 5px gray;
        font-family: 'Trebuchet MS', 'Lucida Sans Unicode', 'Lucida Grande', 'Lucida Sans', Arial, sans-serif;
        z-index: 1;
    }
    button:hover{
        transform: scale(1.25);
    }
    progress{
        height: 50px;
        width: 500px;
        box-shadow: 1px 1px 4px rgba( 0, 0, 0, 0.2 );
        margin-left: -25px;
        position:relative;
        z-index: -0.5;
    }
    .background{
        background-color: rgb(118, 188, 213);
        background-size:cover;
        width: 100%;
        height: 100%;
        margin:auto;
        
        display: flex;
        justify-content: center;
        align-items: center;
        flex-direction: center;

        min-height: 810px;
        min-width: 500px;
    }
    .container{
        background-color: rgb(49, 68, 87,0.6);
        width:80vw;
        height:70vh;
        border-radius: 20px;

        display: flex;

        justify-content: left;
        align-items: left;
        flex-direction: column;

        border-color: rgb(44, 59, 76);
        border-style: solid;
        border-width: 5px;
    }
    .pet-container{
        background-color: rgb(33, 45, 58);
        width:35vw;
        height:55vh;
        border-radius: 20px;
        padding: 5px;
        margin-left: 50px;

        background-size: cover;
        

        border-color:rgb(44, 59, 76);
        border-style: solid;
        border-width: 10px;
    }
    @keyframes scrollBackground {
        from {
            background-position: -2000px 0;
        }
        to {
            background-position: 0 0;
        }
    }
    .scroll_back{
        animation: scrollBackground 10s linear infinite;
        background-repeat :repeat-x;
    }
    .namecontainer{
        width: 30%;
        height: 50px;
        margin-left: 50px;

        border-radius: 10px;
        background-image: url("paper.jpg");
        background-size: 100%;
        border: solid 4px rgb(181, 180, 179);
        font-size: large;
        font-family: 'Trebuchet MS', 'Lucida Sans Unicode', 'Lucida Grande', 'Lucida Sans', Arial, sans-serif;
        margin: top 5px;
    }
    .stats{
        display:grid;
        grid-template-columns: 1fr;
        grid-template-rows: repeat(1fr,4);
        gap:20px;
        width: 100%;
    }
    .stats div {
        width: 100%;
        display: flex;
        align-items: center;
    }
    .stats div progress{
        width: 80%;
    }
    .interact{
        height: 80px;
        width: 80px;
        font-size: 40px;
        border: solid 5px white;
        background-color: rgb(225, 225, 225);
    }
    .petspace{
        display:grid;
        grid-template-columns: auto auto;
        grid-template-rows: auto 1fr;
        justify-content: center;
        align-items: end;
        height: 100%;
    }
    /*
    Source - https://stackoverflow.com/a/18368275
    Posted by nullability, modified by community. See post 'Timeline' for change history
    Retrieved 2026-04-17, License - CC BY-SA 4.0
    */
    progress[value]::-moz-progress-bar { 
        background: rgb(15, 15, 15); 
        height: 40px; 
        border-radius: 50px; 
    }
    progress[value] { 
        background: rgb(134, 22, 22);
        height: 40px; 
        border-radius: 50px; 
        padding: 3px;
        border: solid 3px whitesmoke ;
    }
    progress[value]::-webkit-progress-value {
        height: 40px; 
        border-radius: 50px; 
    }
    progress::-webkit-progress-bar{ /* <---- STANDARD */
        border-radius: 50px;
    }
    
    #hunger[value]::-webkit-progress-value{
        background:rgb(198, 71, 64);
    }
    #cleanliness[value]::-webkit-progress-value{
        background: rgb(94, 165, 201);
    }
    #cleanliness[value]{
        background: rgb(22, 95, 134);
    }
    #energy[value]::-webkit-progress-value{
        background: rgb(207, 162, 76);
    }
    #energy[value]{
        background: rgb(134, 84, 22);
    }
    #wakefullness[value]::-webkit-progress-value{
        background:rgb(169, 135, 204);
    } 
    #wakefullness[value]{
        background: rgb(134, 22, 132);
    }
    .action{
        grid-column: 2;
        grid-row: 2;
        align-self: end;
        height:90px;
    }
    .action:hover{
        transform: scale(1.25);
    }
    #heart{
        height:40px;
        position: relative;
        top:15px;
    }
    #heart:hover{
        transform: scale(1.25);
    }
    #level{
        position: relative;
        font-size: 25px;
        font-family:'Lucida Sans', 'Lucida Sans Regular', 'Lucida Grande', 'Lucida Sans Unicode', Geneva, Verdana, sans-serif;
    }
    #nameplate{
        font-size: 30px;
        font-family: 'Trebuchet MS', 'Lucida Sans Unicode', 'Lucida Grande', 'Lucida Sans', Arial, sans-serif;
        font-weight: bold;
        border-bottom: 1px black solid;
        
        grid-column: 1;
        grid-row: 2;
        align-self: center;
        justify-self: center;
        margin-bottom: 90px;

    }
    #pet{
        width: 204px;
        height: 252px;
        overflow: hidden;
        position: relative;
        grid-column: 1;
        grid-row: 2;
        align-self: end;
    }
    @keyframes moveSpritesheet{
        from{
            transform: translate3d(0px,0,0)
        }
        to{
            transform: translate3d(-408px,0,0)
        }
    }
    .spritesheet{
        animation: moveSpritesheet 1s steps(2) infinite;
        width: 832px;
        height: 2016px;
        margin: auto;
        position: absolute;
    }
    .pixelart{
        image-rendering: pixelated;
    }
    .sitting{
        top: -504px;
    }
    .walking{
        top: -1764px;
    }
    .sleeping{
        filter: brightness(0.5)
    }
    #lamp{
        height: 225px;
    }
    audio{
        display:none;
    }
</style>