<script lang="ts">
    let {
        frontUrl,
        backUrl,
        index,
        animationTime,
    }: {
        frontUrl: string;
        backUrl: string;
        index: number;
        animationTime: number;
    } = $props();

    let currentIndex = $state(index);
    let animationState: undefined | "backward" | "forward" = $state(undefined);

    let timer: number | undefined = undefined;
    $effect(() => {
        // cancel the timer if any exists
        if (timer !== undefined) {
            clearTimeout(timer);
        }

        if (index > currentIndex) {
            animationState = "forward";
        } else if (index < currentIndex) {
            animationState = "backward";
        }

        currentIndex = index;

        setTimeout(() => {
            animationState = undefined;
        }, animationTime);
    });

    let imgWidth: number = $state(0);
</script>

<div
    class="pageRoot"
    class:hidden={animationState === undefined}
    class:flip={animationState !== undefined}
    class:reverse={animationState === "backward"}
    style="--parentWidth:{imgWidth}px; --duration: {animationTime}ms"
>
    <div class="pageSide frontSide" style="--frontUrl: url('{frontUrl}'); --backUrl: url('{backUrl}')" bind:clientWidth={imgWidth}>
        <!-- 8 segments -->
        <div class="pageSegment" style="--idx:0;">
            <div class="pageSegment" style="--idx:1;">
                <div class="pageSegment" style="--idx:2;">
                    <div class="pageSegment" style="--idx:3;">
                        <div class="pageSegment" style="--idx:4;">
                            <div class="pageSegment" style="--idx:5;">
                                <div class="pageSegment" style="--idx:6;">
                                    <div class="pageSegment" style="--idx:7;"></div>
                                </div>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </div>

  
</div>

<style>
    .hidden {
        display: none;
    }

    .pageRoot {
        position: absolute;
        top: 0;
        left: 50%;
        height: 100%;
        width: 50%;
        transform-origin: left;
        transform-style: preserve-3d;
        z-index: 100;
        transition: transform var(--duration) ease-out;
    }

    .pageSide {
        position: absolute;
        top: 0;
        left: 0;
        transform-origin: center;

        width: 100%;
        height: 100%;
        transform-style: preserve-3d;
        backface-visibility: hidden;
    }

    .backSide {
        /* backface-visibility: visible; */

        transform: rotateX(180deg);
    }

    .pageSide > .pageSegment {
        left: 0;
        width: calc(100% / 8);
    }

    .pageSegment {
        height: 100%;

        width: 100%;

        position: absolute;
        top: 0;
        left: 100%;

        transform-style: preserve-3d;
        transform-origin: left;
        background-color: red;
    }

    .pageSegment::before{
        content: "";

        position: absolute;
        width: 100%;
        height: 100%;
        top: 0;
        left: 0;
        
        transform:scaleX(1.02);
        background-image: var(--frontUrl);
        background-size: 800% 100%;
        background-position-x: calc(-1 * var(--idx) * 100%);
        backface-visibility: hidden;
    }

    .pageSegment::after{
        content: "";
        position: absolute;
        width: 100%;
        height: 100%;
        top: 0;
        left: 0;
        
        background-image: var(--backUrl);
        background-size: 800% 100%;
        background-position-x: calc(-1 * (7 - var(--idx)) * 100%);
        transform-origin: center;
        transform: rotateY(180deg) scaleX(1.02);
        backface-visibility: hidden;
    }

    .flip > .pageSide * {
        animation: segmentAnimation var(--duration) 1 ease-out;
    }

    .reverse > .pageSide * {
        animation: reverseSegmentAnimation var(--duration) 1 ease-out;

    }

    


    .pageRoot.flip {
        animation: pageAnimation 1000ms ease-out 1;
    }
    .pageRoot.flip.reverse {
        animation: reversePageAnimation 1000ms ease-out 1;

    }

    @keyframes segmentAnimation {
        0%,
        100% {
            transform: rotateY(0);
        }
        50% {
            transform: rotateY(15deg);
        }
    }

    @keyframes reverseSegmentAnimation {
        0%,
        100% {
            transform: rotateY(0);
        }
        50% {
            transform: rotateY(-15deg);
        }
    }

    @keyframes pageAnimation {
        0% {
            transform: rotateY(0);
        }
        100% {
            transform: rotateY(-180deg);
        }
    }

    @keyframes reversePageAnimation {
        100% {
            transform: rotateY(0);
        }
        0% {
            transform: rotateY(-180deg);
        }
    }
</style>
