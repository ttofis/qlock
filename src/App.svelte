<script>
    // URL Params
    const urlParams = new URLSearchParams(window.location.search);
    let lang = urlParams.get("lang");

    // Setup Characters
    let standard = []
    let letters = []
    let words = []
    let hours = []
    let minutes = []

    // Really big arrays, yes, but at least I don't have to fix up the doubles every time.
    switch(lang) {
        case "el":
            standard = ["Η", "ΩΡΑ", "ΕΙΝΑΙ"]
            words = [["Η", "Η"],["Χ", ""],["ΩΡΑ","ΩΡΑ"],["Τ",""],["ΕΙΝΑΙ","ΕΙΝΑΙ"],["ΜΙΑ","ΜΙΑ"],["ΔΥΟ","ΔΥΟ"],["ΤΡΕΙΣ","ΤΡΕΙΣ"],["ΤΕΣΣΕΡΙΣ","ΤΕΣΣΕΡΙΣ"],["ΕΞΙ","ΕΞΙ"],["ΠΕΝΤΕ","ΠΕΝΤΕ"],["Ρ",""],["ΟΧΤΩ","ΟΧΤΩ"],["Η",""],["ΕΦΤΑ","ΕΦΤΑ"],["Ε",""],["ΕΝΤΕΚΑ","ΕΝΤΕΚΑ"],["ΔΩΔΕΚΑ","ΔΩΔΕΚΑ"],["ΕΝΝΙΑ","ΕΝΝΙΑ"],["ΔΕΚΑ","ΔΕΚΑ"],["Χ",""],["ΠΑΡΑ","ΠΑΡΑ"],["ΕΡ",""],["ΚΑΙ","ΚΑΙ"],["Ε",""],["ΤΕΤΑΡΤΟ","ΤΕΤΑΡΤΟ"],["ΕΙΚΟΣΙ","ΕΙΚΟΣΙ"],["Η",""],["ΔΕΚΑ","-ΔΕΚΑ"],["ΜΙΣΗ","ΜΙΣΗ"],["Ε",""],["ΠΕΝΤΕ","-ΠΕΝΤΕ"],["Ρ",""]]
            hours = ["ΔΩΔΕΚΑ", "ΜΙΑ", "ΔΥΟ", "ΤΡΕΙΣ", "ΤΕΣΣΕΡΙΣ", "ΠΕΝΤΕ", "ΕΞΙ", "ΕΦΤΑ", "ΟΧΤΩ", "ΕΝΝΙΑ", "ΔΕΚΑ", "ΕΝΤΕΚΑ"]
            minutes = [[],["-ΠΕΝΤΕ", "ΚΑΙ"],["-ΔΕΚΑ", "ΚΑΙ"],["ΤΕΤΑΡΤΟ", "ΚΑΙ"],["ΕΙΚΟΣΙ", "ΚΑΙ"],["ΕΙΚΟΣΙ", "-ΠΕΝΤΕ", "ΚΑΙ"],["ΜΙΣΗ", "ΚΑΙ"],["ΕΙΚΟΣΙ", "-ΠΕΝΤΕ", "ΠΑΡΑ"],["ΕΙΚΟΣΙ", "ΠΑΡΑ"],["A", "ΤΕΤΑΡΤΟ", "ΠΑΡΑ"],["-ΔΕΚΑ", "ΠΑΡΑ"],["-ΠΕΝΤΕ", "ΠΑΡΑ"]]
            break;
        default:
            lang = "en";
            standard = ["IT", "IS"]
            words = [["IT","IT"],["L",""],["IS","IS"],["ASAMPM",""],["A","A"],["C",""],["QUARTER","QUARTER"],["DC",""],["TWENTY","TWENTY"],["FIVE","-FIVE"],["X",""],["HALF","HALF"],["S",""],["TEN","-TEN"],["F",""],["TO","TO"],["PAST","PAST"],["ERU",""],["NINE","NINE"],["ONE","ONE"],["SIX","SIX"],["THREE","THREE"],["FOUR","FOUR"],["FIVE","FIVE"],["TWO","TWO"],["EIGHT","EIGHT"],["ELEVEN","ELEVEN"],["SEVEN","SEVEN"],["TWELVE","TWELVE"],["TEN","TEN"],["SE",""],["OCLOCK","OCLOCK"]],hours = ["TWELVE", "ONE", "TWO", "THREE", "FOUR", "FIVE", "SIX", "SEVEN", "EIGHT", "NINE", "TEN", "ELEVEN"]
            minutes = [["OCLOCK"],["-FIVE", "PAST"],["-TEN", "PAST"],["A", "QUARTER", "PAST"],["TWENTY", "PAST"],["TWENTY", "-FIVE", "PAST"],["HALF", "PAST"],["TWENTY", "-FIVE", "TO"],["TWENTY", "TO"],["A", "QUARTER", "TO"],["-TEN", "TO"],["-FIVE", "TO"]]
            break;
    }

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
        for (let c of w[0]) {
            curr.push({"letter": c, "parent": w[1], "glow": false})
        }
        c += w[0].length
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

    // O' detail
    if (lang === "en") {
        letters[11][7].letter = "O'"
    }

    // Time set function
    function set() {
        reset();
        for(let x of standard) {
            toggle(x, true);
        }
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
        {#if letters.length != 0}
        {#each letters as line}
            {#each line as letter}
                <div class="letter" class:glow={letter.glow}>
                    <p>{letter.letter}</p>
                </div>
            {/each}
        {/each}
        {/if}
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
