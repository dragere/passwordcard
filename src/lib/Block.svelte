<script lang="ts">
    import {permuts} from "$lib/util";

    export let input: string;
    export let output: string[];

    export let colorOffsetIndex: number;
    export let seed: number;




    $: cells = output.slice(0, 4).concat(input, output.slice(-4))

    let generateColors = (numseed: number) => {
        let out: string[] = [];
        for (const i of permuts[(colorOffsetIndex+1)*numseed%permuts.length]){
            // out.push([
            //     "#d77691",
            //     "#de6e3e",
            //     "#c0ab5e",
            //     "#90d24c",
            //     "#73c796",
            //     "#94b2be",
            //     "#9689d8",
            //     "#d254d6"
            // ][i])
            // out.push([
            // '#F2C4DE',
            // '#c8eeb2',
            // '#E6E6FA',
            // '#FFDAB9',
            // '#AED8E6',
            // '#FFFACD',
            // '#C8A2C8',
            // '#F08080'
            // ][i])
            out.push(`hsl(${360 / 8 * i}deg 70% 80%);`)
        }
        out = out.slice(0, 4).concat("#fff", ...out.slice(-4))
        return out
    }

    $: colors = generateColors(seed)
</script>

<div class="block">
    {#each colors as c, i }
        <div class="cell"
             style="background-color: {c}; {i === 4 ? 'font-weight: 900;font-size: 9pt;': ''}">{cells[i]}</div>
    {/each}
</div>

<style>
    .block {
        display: grid;
        grid-template-columns: repeat(3, 1fr);
        grid-gap: 0;

        border: 1px solid black;
        width: min-content;
    }

    .cell {
        font-family: monospace;
        width: 12px;
        height: 12px;
        /*margin: 2px;*/
        text-align: center;
        display: flex;
        justify-content: center;
        align-items: center;
        border-collapse: collapse;
        font-size: 7pt;
    }

</style>
