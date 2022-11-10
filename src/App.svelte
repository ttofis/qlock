<script>
    // Setup Characters
    var words = ["IT","L","IS","ASAMPM","A","C","QUARTER","DC","TWENTY","FIVE","X","HALF","S","TEN","F","TO","PAST","ERU","NINE","ONE","SIX","THREE","FOUR","FIVE","TWO","EIGHT","ELEVEN","SEVEN","TWELVE","TEN","SE","OCLOCK"]
    var letters = []
    var hours = ["TWELVE", "ONE", "TWO", "THREE", "FOUR", "FIVE", "SIX", "SEVEN", "EIGHT", "NINE", "TEN", "ELEVEN"]
    var minutes = [["OCLOCK"],["TFIVE", "PAST"],["TTEN", "PAST"],["A", "QUARTER", "PAST"],["TWENTY", "PAST"],["TWENTY", "TFIVE", "PAST"],["HALF", "PAST"],["TWENTY", "TFIVE", "TO"],["TWENTY", "TO"],["A", "QUARTER", "TO"],["TTEN", "TO"],["TFIVE", "TO"]]

    let c = 0
    let curr = []

    curr.push({"letter":"•", "parent":"min1", "glow": false})
    for (let i = 0; i < 13; i++) {
        curr.push({"letter":"", "parent":"", "glow": false})
    }
    curr.push({"letter":"•", "parent":"min2", "glow": false})
    letters.push(curr)
    curr = []
    for (let i = 0; i < 15; i++) {
        curr.push({"letter":"", "parent":"", "glow": false})
    }
    letters.push(curr)
    curr = []
    for (let w of words) {
        if (c == 0) {
            curr.push({"letter": "", "parent": "", "glow": false})
            curr.push({"letter": "", "parent": "", "glow": false})
        }
        for (let c of w) {
            curr.push({"letter": c, "parent": w, "glow": false})
        }
        c += w.length
        if (c == 11) {
            curr.push({"letter": "", "parent": "", "glow": false})
            curr.push({"letter": "", "parent": "", "glow": false})
            letters.push(curr)
            curr = []
            c = 0
        }
    }
    for (let i = 0; i < 15; i++) {
        curr.push({"letter":"", "parent":"", "glow": false})
    }
    letters.push(curr)
    curr = []
    curr.push({"letter":"•", "parent":"min4", "glow": false})
    for (let i = 0; i < 13; i++) {
        curr.push({"letter":"", "parent":"", "glow": false})
    }
    curr.push({"letter":"•", "parent":"min3", "glow": false})
    letters.push(curr)

    // Corrections - yes, for loops are a waste
    // Correct first to second five difference
    let count = 0;
    for (let line of letters) {
        for (let l of line) {
            if (l.parent == "FIVE") {
                l.parent = "TFIVE";
                count++;
            }
        }
        if (count == 4) break;
    }
    // Correct first to second ten difference
    count = 0;
    for (let line of letters) {
        for (let l of line) {
            if (l.parent == "TEN") {
                l.parent = "TTEN";
                count++;
            }
        }
        if (count == 3) break;
    }

    // O' detail
    letters[11][7].letter = "O'"

    // Time set function
    function set() {
        reset();
        toggle("IT", true);
        toggle("IS", true);
        let d = new Date();
        let h = d.getHours();
        let m = d.getMinutes();

        if (m >= 35) h += 1;
        h = h%12;
        let minsOver5 = m%5;
        m -= minsOver5;
        m = m/5;

        toggle(hours[h], true)
        for (let x of minutes[m]) {
            toggle(x, true)
        }
        switch(minsOver5) {
            case 4:
                toggle("min1", true)
                toggle("min2", true)
                toggle("min3", true)
                toggle("min4", true)
                break;
            case 3:
                toggle("min1", true)
                toggle("min2", true)
                toggle("min3", true)
                break;
            case 2:
                toggle("min1", true)
                toggle("min2", true)
                break;
            case 1:
                toggle("min1", true)
                break; 
        }
        letters = letters; // Stupidity
    }

    function toggle(s, state) {
        for (let line of letters) {
            for (let letter of line) {
                if (letter.parent == s) {
                    letter.glow = state;
                }
            }
        }
    }

    function reset() {
        for (let line of letters) {
            for (let letter of line) {
                letter.glow = false;
            }
        }
    }
    
    // Time sync and loop
    async function run () {
        let d = new Date();
        let sec = d.getSeconds();
        set();
        setTimeout(()=>{
            set();
            setInterval(()=>{
                set();
            }, 60 * 1000);
        }, (60 - sec) * 1000);
    }

    run();
</script>

<main>
    <div class="clock">
        {#each letters as line}
            {#each line as letter}
                <div class="letter" class:glow={letter.glow}>
                    <p>{letter.letter}</p>
                </div>
            {/each}
        {/each}
    </div>
</main>

<style>
    main {
        display: flex;
        justify-content: center;
        align-items: center;
        height: 100vh;
    }
    .clock {
        font-family: 'Stick No Bills', sans-serif;
        height: min(90vh, 90vw);
        width: min(90vh, 90vw);
        display: grid;
        grid-template-columns: repeat(15, calc(100%/15));
    }
    .letter {
        aspect-ratio: 1;
        display: flex;
        align-items: center;
        justify-content: center;
    }
    .letter p {
        font-size: min(5vh,5vw);
        color: #070707;
        transition: color 0.5s;
    }
    .glow p {
        color: white;
    }
</style>
