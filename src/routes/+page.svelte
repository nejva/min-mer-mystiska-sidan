<script>

    let hunger = 100
    let cleanliness = 100
    let energy = 80
    let wakefullness = 100
    let bonding = 1

    let action_icon = ""
    let display = "none"
    let b_display = "none"
    let scroll_direction = ""
    let scroll_anim = false

    let bathroom = "Bathroom.png"
    let standard = "StandardRoom.png"
    let garden = "Backyard2.png"
    let bedroom = "Bedroom.png"
    let sidewalk = "Sidewalk.png"

    let petname = "🐾 Rename..."
    let skin = "Goldie_v02.png"
    let background = standard

    /*  ----------------------------------------------------------------------  */ 

    let new_name;
    function Rename(){
        new_name = petname
    }

    function Clean(){
        background = bathroom
        action_icon = "🧼"
        display = "inline"
        b_display = "none"
        scroll_anim = false
    } function Feed(){
        background = standard
        action_icon = "🍖"
        display = "none"
        b_display = "inline"
        scroll_anim = false
    } function Sleep(){
        background = bedroom
        action_icon = "💤"
        display = "inline"
        b_display = "none"
        scroll_anim = false
    } function Outside(){
        //*const sprite = document.querySelector('.spritesheet.pixelart');
        //*sprite.classList.add('sit');

        background = garden
        action_icon = "🦮"
        display = "inline"
        b_display = "none"
        scroll_anim = false
    }

    function Action(){
        if (background===bathroom){
            cleanliness += 40
        } 
        else if(background===standard){
            b_display = "inline"
            hunger += 40
        } 
        else if(background===garden){
            background = sidewalk
            scroll_anim = true
            scroll_direction = "repeat-x"

            energy -= 40
            wakefullness -= 10
        } 
        else if(background===bedroom){
            /*
            Background switch darkmode
            Animation -> Sleep
            Snoring
            Timer
            */
            energy += 50
            wakefullness = 100
        }
    }

    /* https://www.geeksforgeeks.org/javascript/how-to-select-a-random-element-from-array-in-javascript/ */
    let all_moods = ["hunger","cleanliness","energy","wakefullness"];
    const getRandomIndex = (arr) => Math.floor(Math.random() * arr.length);

    
    function drainMood(){
        let mood = all_moods[getRandomIndex(all_moods)];

        const min = 2000
        const max = 10000
        let r_time = Math.floor(Math.random() * (max - min + 1)) + min;
        
        setTimeout(()=>{
            if (mood === "hunger" && hunger > 0)
                hunger -= 15;
            if (mood === "cleanliness" && cleanliness > 0)
                cleanliness -= 15;
            if (mood === "energy" && energy < 100)
                energy += 15;
            if (mood === "wakefullness" && wakefullness > 0)
                wakefullness -= 10;

            drainMood();
        }, r_time);
    }
    drainMood()
    
    let interval
    function BondingTimer(){
        let mood_tot = (hunger + cleanliness + energy + wakefullness) / 4;

        if (mood_tot>50 && !interval){
            interval = setInterval(()=>{ 
                bonding += 1
            }, 5000);
        }else if(mood_tot<50 && interval){
            clearInterval(interval)
            interval = undefined
        }
    }
    setInterval(()=>BondingTimer(),1000 )
    

</script>

<main class="background">
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
                        <img class="spritesheet pixelart" src={skin} alt="Pet">
                    </div>
                </div>
                <img id="bowl" class="pixelart" src="BowlFull.png" alt="" style="display:{b_display};">
            </div>

            <div class="stats">
                <div>
                    <button class="interact" on:click={()=>Feed()}>🍽️</button>
                    <progress id="hunger" value="{hunger}" max="100" min="0"></progress>
                </div>
                <div>
                    <button class="interact" on:click={()=>Clean()}>🛁</button>
                    <progress id="cleanliness" value="{cleanliness}" max="100" min="0"></progress>
                </div>
                <div>
                    <button class="interact" on:click={()=>Outside()}>⚡</button>
                    <progress id="energy" value="{energy}" max="100" min="0"></progress>
                </div>
                <div>
                    <button class="interact" on:click={()=>Sleep()}>🌘</button>
                    <progress id="wakefullness" value="{wakefullness}" max="100" min="0"></progress>
                </div>

                <button class="interact" id="action" style="display: {display};" on:click={()=>Action()}>{action_icon}</button>
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
            background-position: 0 0;
        }
        to {
            background-position: -2000px 0;
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
        border: solid 4px rgb(107, 101, 92);
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
        
    }
    .petspace{
        display:flex;
        flex-direction: column;
        align-items: center;
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
    #action{
        height: 100px;
        width: 100px;
        bottom: 250px;
        right: 560px;
        font-size: 55px;
    }
    #bowl{
        height:90px;
        position:relative;
        bottom: -60px;
        left: 330px;
        
    }
    #heart{
        height:40px;
        position: relative;
        top:15px;
    }
    #level{
        position: relative;
        font-size: 25px;
        font-family:'Lucida Sans', 'Lucida Sans Regular', 'Lucida Grande', 'Lucida Sans Unicode', Geneva, Verdana, sans-serif;
    }
    #nameplate{
        position: relative;
        top: 200px;
        font-size: 30px;
        font-family: 'Trebuchet MS', 'Lucida Sans Unicode', 'Lucida Grande', 'Lucida Sans', Arial, sans-serif;
        font-weight: bold;
        border-bottom: 1px black solid;
    }

    /* https://www.youtube.com/watch?v=ekI7vjkFrGA */
    #pet{
        width: 204px;
        height: 252px;
        overflow: hidden;
        position:relative;
        bottom: -190px;
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
    .sit{
        top: -504px;
    }
    .walk{
        top: -1764px;
    }
</style>