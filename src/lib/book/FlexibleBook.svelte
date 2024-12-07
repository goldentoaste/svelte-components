<script lang="ts">
    import FlexibleBookPage from "./FlexibleBookPage.svelte";

    interface BookType {
        imgUrls: string[];
        index: number;
        singlePageMode: boolean;
    }

    interface Page {
        frontPage: string;
        backPage?: string;
        index: number;
    }

    let { imgUrls, index = $bindable(), singlePageMode = false }: BookType = $props();

    let pages: Page[] = $derived.by(() => {
        if (singlePageMode) {
            return imgUrls.map((item, index) => ({
                frontPage: item,
                backPage: undefined,
                index: index,
            }));
        }

        const out: Page[] = [];

        for (let i = 0; i < Math.ceil(imgUrls.length / 2); i++) {
            out.push({
                frontPage: imgUrls[i * 2],
                backPage: imgUrls[i * 2 + 1], // will be undefined if out of bound.,
                index: i,
            });
        }

        return out;
    });

    let prevUrl: string | undefined = $state();
    let nextUrl: string | undefined = $state();

    let transitionPage: Page | undefined = $state();

    const transitionTime = 500;
    export function nextPage() {
        if (transitionPage) {
            return; // block transition if there already one.
        }

        const nextPage = pages[index + 1];
        nextUrl = nextPage ? nextPage.frontPage : "";
        index += 1; // trigger flip animation

        setTimeout(() => {
            prevUrl = transitionPage?.backPage;
            transitionPage = undefined;
        }, transitionTime);
    }

    export function prevPage() {
        const prevPage = pages[index - 1];
        prevUrl = prevPage ? prevPage.backPage : "";
        index -= 1;

        setTimeout(() => {
            nextUrl = transitionPage?.frontPage;
            transitionPage = undefined;
        }, transitionTime);
    }
</script>

<div class="bookRoot">
    <FlexibleBookPage
        frontUrl={transitionPage ? transitionPage.frontPage : ""}
        backUrl={transitionPage ? (transitionPage.backPage ?? "") : ""}
        {index}
    ></FlexibleBookPage>

    <div class="prevPage" style="--background: url({prevUrl})"></div>

    <div class="nextPage" style="--background: url({nextUrl})"></div>
</div>

<style>
    .bookRoot {
        width: 100%;
        height: 100%;
    }

    .prevPage {
        left: 0;
    }

    .nextPage {
        left: 50%;
    }

    .prevPage,
    .nextPage {
        width: 50%;
        height: 100%;

        position: absolute;
        top: 0;
        background-image: var(--background);
    }
</style>
