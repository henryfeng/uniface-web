<script lang="ts">

    import ActionsRow from "./ActionsRow.svelte";
    import type ActionsColumn from "../lib/ActionsColumn";
    import type TableRow from "./TableRow";
    import {onDestroy, onMount} from "svelte";

    export let actionsColumn: ActionsColumn;
    export let scrollTop: number = 0;
    export let rows: Array<TableRow>;

    export let inlineRowHeight: number = 0;

    export let expandRow: TableRow;

    export let rowHeight: number;

    const handleActionPanelScroll = (e: Event) => {
        scrollTop = e.target?.scrollTop;
    }

    let scrollPanel: any;

    $: if (scrollTop > -1) {
        if (scrollPanel) {
            scrollPanel.scrollTop = scrollTop
        }
    }

    let resizeObserver: ResizeObserver;
    let panel: any;
    let rect: DOMRect;

    onMount(async () => {
        resizeObserver = new ResizeObserver(() => {
            rect = panel.getBoundingClientRect();
        });
        resizeObserver.observe(panel);
    });

    onDestroy(() => {
        resizeObserver.disconnect();
    });


</script>

<div class="action-panel" bind:this={panel} style="user-select: none; width: {actionsColumn.width}px;">
    <div bind:this={scrollPanel} class="scroll-panel" on:scroll|passive={handleActionPanelScroll}>
        <div>
            {#each rows as row, idx}
                <ActionsRow {row} {rowHeight} parentRect={rect} alternative={idx % 2 == 1} width={actionsColumn.width}
                            actions={actionsColumn.getActions(row.data)}/>
                {#if row == expandRow}
                    <div class="inline-panel" style="height: {inlineRowHeight??0}px">
                    </div>
                {/if}
            {/each}
        </div>
    </div>
    <div class="bottom-mask-overlay">
        <!-- 用于匹配中间数据区域的滚动条 -->
    </div>
</div>