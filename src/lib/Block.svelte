<script lang="ts">
    import {permuts} from "./util";

    export let input: string;
    export let output: string[];

    export let colorOffsetIndex: number;
    export let seed: number;

    export let colors: {
        bg: string[],
        fg: string[]
    }


    $: cells = output.slice(0, 4).concat(input, output.slice(-4))

    let generateColors = (numseed: number, col: {
        bg: string[],
        fg: string[]
    }) => {
        let bg: string[] = [];
        let fg: string[] = [];
        let center_bg = col.bg[0];
        let center_fg = col.fg[0];
        let bg_cols = col.bg.slice(1);
        let fg_cols = col.fg.slice(1);

        for (const i of permuts[(colorOffsetIndex+1)*numseed%permuts.length]){
            bg.push(bg_cols[i]);
            fg.push(fg_cols[i]);
        }
        bg = bg.slice(0, 4).concat(center_bg, ...bg.slice(-4))
        fg = fg.slice(0, 4).concat(center_fg, ...fg.slice(-4))
        return [bg,fg];
    }

    $: [colors_bg, colors_fg] = generateColors(seed, colors);
</script>

<div class="block">
    {#each colors_bg as c, i }
        <div class="cell"
             style="background-color: {c};
             color: {colors_fg[i]};
{i === 4 ? 'font-weight: 900;font-size: 9pt;': ''}">{cells[i]}</div>
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
