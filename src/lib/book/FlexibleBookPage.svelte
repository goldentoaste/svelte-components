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
    <div class="pageSide frontSide" style="--imgUrl: url('{frontUrl}'); " bind:clientWidth={imgWidth}>
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

    <div class="pageSide backSide" style="--imgUrl:url('{backUrl}');">
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
        /* display: none; */
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
    }

    .pageSegment {
        height: 100%;
        --width: calc(var(--parentWidth) / 8);
        width: var(--width);

        position: absolute;
        top: 0;
        left: 100%;

        transform-style: preserve-3d;
        transform-origin: left;

        transition: transform var(--duration) ease-out;
        background-size: var(--parentWidth) 100%;
        /* background-size: 400px 400px; */

        background-position-x: calc(-1 * var(--idx) * var(--parentWidth) / 8);
        background-image: var(--imgUrl);
        backface-visibility: hidden;
    }

    .flip > .pageSide * {
        animation: segmentAnimation var(--duration) 1 ease-out;
    }

    .flip.reverse > .pageSide * {
        animation: reverseSegmentAnimation;
    }

    .pageRoot.flip {
        animation: pageAnimation 1000ms ease-out 1;
    }
    .pageRoot.flip.reverse {
        animation-direction: reverse;
    }

    @keyframes segmentAnimation {
        0%,
        100% {
            transform: rotateY(0);
        }
        50% {
            transform: rotateY(5deg);
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
</style>
