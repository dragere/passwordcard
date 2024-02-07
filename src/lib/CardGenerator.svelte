<script lang="ts">
    import {getRNG} from "$lib/util";
    import Block from "$lib/Block.svelte";

    let generateOutput = (dict: string[], colNum: number, chunkSize: number): string[][] => {
        if (dict.length === 0) dict = [" "]
        let out = []
        for (let j = 0; j < colNum; j++) {
            let outChunk = []
            for (let k = 0; k < chunkSize; k++) {
                outChunk.push(dict[Math.floor(dict.length * rng())])
            }
            out.push(outChunk)
        }
        return out
    }
    let generateSeed = () => {
        seed = Math.random().toString(36).slice(2, 2 + 16)
    }

    let iterChunks = (iter: string | any[], len: number) => {
        let chunks = [];
        let splitter: Function;
        if (typeof iter === "string") {
            splitter = (str: string, start: number, end: number) => {
                return str.substring(start, end)
            }
        } else {
            splitter = (iter: string, start: number, end: number) => {
                return iter.slice(start, end)
            }
        }
        for (let i = 0, charsLength = iter.length; i < charsLength; i += len) {
            chunks.push(splitter(iter, i, i + len));
        }
        return chunks;
    }

    let classNames = {
        ",.;:!?-+*%/&'\"#()": "easy qwertz extra",
        "abcdefghijklmnopqrstuvwxyz": "lowercase latin",
        "ABCDEFGHIJKLMNOPQRSTUVWXYZ": "uppercase latin",
        "0123456789": "digits",
        "äöüÄÖÜ": "umlauts",
        ",;.:!?\"\'": "punctuation",
        "([{}])": "parenthesis",
        "+-*/<>=%": "math",
        "|_#~&$§@€": "special"
    }

    let classes = Object.keys(classNames)

    let input = "05ABCDEF16GHIJKL27MNOPQR38STUVWX49YZ?"
    let extraOptions = false;
    let cardborder = true;

    let customDict = ""
    let exclude = ""
    let dict = ["abcdefghijklmnopqrstuvwxyz", "ABCDEFGHIJKLMNOPQRSTUVWXYZ", "0123456789", ",.;:!?-+*%/&'\"#()"]
    let seed: string = "change me 😀"

    let color_options = [
        {
            fg: [...Array(8)].fill(`#000`),
            bg: [...Array(8)].map((_, i) => `hsl(${360 / 8 * i}deg 70% 80%);`)
        },
        {
            fg: [...Array(8)].fill(`#000`),
            bg: [
                "#d77691",
                "#de6e3e",
                "#c0ab5e",
                "#90d24c",
                "#73c796",
                "#94b2be",
                "#9689d8",
                "#d254d6"
            ]
        },
        {
            fg: [...Array(8)].fill(`#000`),
            bg: [
                '#F2C4DE',
                '#c8eeb2',
                '#E6E6FA',
                '#FFDAB9',
                '#AED8E6',
                '#FFFACD',
                '#C8A2C8',
                '#F08080'
            ]
        }
    ]
    let color_choice = 2;

    $: rng = getRNG(seed);
    $: numseed = Math.floor(rng() * 15559) /// used for the colors each block
    $: inputList = Array.from(input)
    $: fullDict = Array.from(new Set(Array.from(dict.join('') + customDict).filter(c => !exclude.includes(c))))
    $: out = generateOutput(fullDict, inputList.length, 8);

    console.log(color_options[color_choice])
</script>


<div class="settings">
    <label class="text-label" for="seed">seed: <input id="seed" class="text-input" bind:value={seed}
                                                      placeholder="seed"/>
        <button on:click={generateSeed}>generate</button>
    </label>
    <div>
        <label><input type="checkbox" bind:checked={extraOptions}/>options</label>
        <label><input type="checkbox" bind:checked={cardborder}/>show credit card border <span style="font-size: 7pt">(ISO/IEC 7810 ID-1)</span></label>
    </div>
    {#if extraOptions}
        <label class="text-label" for="input">input:
            <input id="input" class="text-input" bind:value={input} placeholder="input"/>
        </label>
        <label class="text-label" for="custom">custom characters:
            <input id="custom" class="text-input" bind:value={customDict} placeholder="custom characters"/>
        </label>
        <label class="text-label" for="excluded">excluded characters:
            <input id="excluded" class="text-input" bind:value={exclude} placeholder="excluded characters"/>
        </label>

        {#each classes as cls}
            <label title="{cls}">
                <input type="checkbox" bind:group={dict} value={cls}/>
                {classNames[cls]}
            </label>
        {/each}

    {/if}
</div>
<div class="card">
    <div class:cardborder></div>
    <div class="blocks">
        {#each out as block,i}
            <div class="block-wrapper">
                <Block input={inputList[i]} output={block} colorOffsetIndex={i} seed={numseed} colors={color_options[color_choice]}/>
            </div>
        {/each}
        <div class="block-wrapper" style="width: {39.333 * Math.max(3,8-input.length%8) - 2}px">
            <div id="seedDisplay">{seed}</div>
            <div id="fullDict">{fullDict.join("")}</div>
        </div>
    </div>
</div>


<style>

    .cardborder {
        border: 1px black dashed;
        /*https://en.wikipedia.org/wiki/ISO/IEC_7810*/
        width: 85.6mm;
        height: 53.98mm;
        border-radius: 3mm;
        position: absolute;
        z-index: 0;
    }

    .card {
        width: 85.6mm;
        min-height: 53.98mm;
        /*border-radius: 3mm;*/
        /*border: 1px transparent;*/
        page-break-inside: avoid;
    }


    .blocks {
        display: flex;
        flex-wrap: wrap;
        justify-content: flex-start;
        align-content: flex-start;
        flex-direction: row;
        margin-top: 2px;
        margin-left: 2px;
        padding-top: 3px;
        padding-left: 1px;
        box-sizing: border-box;
        overflow-x: visible;
        z-index: 1;
        position: relative;
    }

    .block-wrapper {
        margin: 1px;
    }

    .text-input {
        width: 100%;
        box-sizing: border-box;
        font-size: 11pt;
        font-family: monospace;
    }

    .text-label {
        font-size: 10pt;
        margin-top: 0.5rem;
        background-color: #d2e8d7;
        border-radius: 2px;
        padding: 2px;
    }


    .settings {
        display: flex;
        flex-direction: column;
    }

    #seedDisplay {
        font-family: monospace;
        font-size: xx-small;
        background-color: grey;
        width: fit-content;
        border-radius: 1px;
        white-space: pre;
    }

    #fullDict {
        width: 100%;
        font-family: monospace;
        font-size: 5pt;
        font-weight: 1;
        overflow-wrap: anywhere;
        word-wrap: anywhere;
        /*hyphens: auto;*/

    }

    @media print {
        .settings {
            display: none !important;
        }

    }
</style>
