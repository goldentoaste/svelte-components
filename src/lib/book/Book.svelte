<script lang="ts" generics="T">
    import type { Snippet } from "svelte";

    export interface BookData<T> {
        contentProps: T[];
        pageContent: Snippet<[T]>;
        singlePageMode?: boolean;
        minPageWidth: number;
        minPageHeight: number;
        pageIndex: number;
    }

    interface PageData<T> {
        pageId: number;
        front: T;
        back: T | undefined;
    }

    let {
        contentProps,
        pageContent,
        singlePageMode = false,
        minPageWidth,
        minPageHeight,
        pageIndex = $bindable(0),
    }: BookData<T> = $props();

    let pageProps = $derived.by<PageData<T>[]>(() => {
        if (singlePageMode) {
            return contentProps.map((item, index) => ({ front: item, back: undefined, pageId: index }));
        } else {
            const out: PageData<T>[] = [];

            for (let i = 0; i < Math.ceil(contentProps.length / 2); i++) {
                out.push({
                    pageId: i,
                    front: contentProps[i * 2],
                    back: contentProps[i * 2 + 1], // will be undefined is out of bound
                });
            }

            return out;
        }
    });
</script>

<div class="bookRotationRoot">
    <div
        class="bookRoot"
        style={`
--minPageWidth:${minPageWidth}px; --minPageHeight:${minPageHeight}px;
`}
    >
        <div class="pageContainer">
            {#each pageProps as { front, back, pageId } (pageId)}
                <!-- only render stuff immediately visible -->
                <!-- but it looks better when everything renders :/ -->
                {#if Math.abs(pageId - pageIndex) <= 2 || true}
                    <!-- reverse z index -->
                    {@const flipped = pageId < pageIndex}
                    {@const zIndex = flipped ? pageId : pageProps.length - pageId}
                    <div class:flipped class="pageRotationRoot" style="--zIdx:{zIndex}">
                        {#if front !== undefined}
                            <div class="pageContent">
                                {@render pageContent(front)}
                            </div>
                        {:else}
                            <div></div>
                        {/if}

                        {#if back !== undefined}
                            <div class="pageContent oddPage">
                                {@render pageContent(back)}
                            </div>
                        {:else}
                            <div></div>
                        {/if}
                    </div>
                {/if}
            {/each}
        </div>
    </div>
</div>

<style>
    .bookRotationRoot {
        min-width: fit-content;
        width: 100%;
        min-height: fit-content;
        height: 100%;

        perspective: 500px;
        perspective-origin: 50% 100%;
        transform-style: preserve-3d;
    }

    .bookRoot {
        position: relative;
        background-color: brown;
        padding: 1rem;

        /* enable 3d transform */
        transform-style: preserve-3d;
        transform: rotateX(30deg);
    }

    .pageContainer {
        position: relative;
        background-color: lightgoldenrodyellow;
        width: 100%;
        min-width: calc(var(--minPageWidth) * 2);
        height: 100%;
        min-height: calc(var(--minPageHeight));
        transform-style: preserve-3d;
    }

    .pageContainer::after {
        content: "";
        position: absolute;
        top: 0;
        left: 50%;
        width: 1px;
        height: 100%;
        background-color: black;
    }

    .pageRotationRoot {
        width: 50%;
        height: 100%;

        position: absolute;
        left: 50%;
        top: 0;
        z-index: var(--zIdx);

        transform-origin: top left;
        transform-style: preserve-3d;

        transition: transform 900ms ease-out;
    }

    .pageContent {
        transform-origin: 50% 50%;
        transform-style: preserve-3d;

        position: absolute;
        top: 0;
        left: 0;
        width: 100%;
        height: 100%;

        /* backface culling */
        background-color: white;
        backface-visibility: hidden;
    }

    .pageContent.oddPage {
        /* flip page */
        transform: rotateY(180deg);
    }

    .pageRotationRoot.flipped {
        transform: rotateY(-180deg);
    }
</style>
