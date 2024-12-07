<script lang="ts">
    import Book from "$lib/book/Book.svelte";

    const images = [
        { img: "/1.jpg", desc: "Chaos fusion" },
        { img: "/2.jpg", desc: "Archnemeses Protos" },
        { img: "/3.jpg", desc: "Number 27" },
        { img: "/4.jpg", desc: "Graceful tear" },
        { img: "/5.jpg", desc: "Plunder Lys" },
        { img: "/6.jpg", desc: "Wicked Avatar" },
        { img: "/7.jpg", desc: "Limiter Removal" },
        { img: "/8.jpg", desc: "That one guy that doesnt die by battle, and makes opponent discard one when attacks." },
        { img: "/9.jpg", desc: "Dusk dragon" },
        { img: "/10.jpg", desc: "Skystriker Raye" },
        { img: "/11.jpg", desc: "Creature swap" },
        { img: "/12.jpg", desc: "Edlitch Goldenland" },
    ];

    let pageIndex = $state(0);

    function nextPage() {
        if (pageIndex < images.length / 2) {
            pageIndex += 1;
        }
    }

    function prevPage() {
        if (pageIndex > 0) {
            pageIndex -= 1;
        }
    }
</script>

<!-- Markup -->

<h2>My test page.</h2>

{#snippet pageContent({ img, desc }: { img: string; desc: string })}
    <img src={img} alt="" width="200" height="200" />
    <h2>{desc}</h2>
{/snippet}

<div class="bookParent">
    <Book contentProps={images} {pageContent} minPageHeight={300} minPageWidth={200} bind:pageIndex></Book>
</div>

<p>Current page index: {pageIndex} / {Math.ceil(images.length / 2)}</p>
<div class="buttonGroup">
    <button onclick={prevPage}>Prev page</button>

    <button onclick={nextPage}>Next page</button>
</div>

<style>
    .bookParent {
        display: flex;
        width: fit-content;
        margin-left: 5rem;
    }

    .buttonGroup {
        position: relative;
        z-index: 100;
    }
</style>
