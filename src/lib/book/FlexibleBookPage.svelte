<script lang="ts">
    let {
        frontUrl,
        backUrl,
        index,
    }: {
        frontUrl: string;
        backUrl: string;
        index: number;
    } = $props();

    let currentIndex = $state(index);
    let animationState: undefined | "backward" | "forward" = $state(undefined);

    const animationTime = 500; // ms
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
    });

    $inspect(animationState);
</script>

<div
    class="pageRoot"
    class:hidden={animationState === undefined}
    class:flip={animationState !== undefined}
    class:reverse={animationState === "backward"}
>
    <div class="frontSide" style="--imgUrl: {frontUrl};">
        <!-- 8 segments -->
        <div class="pageSegment" style="--idx:1;">
            <div class="pageSegment" style="--idx:2;">
                <div class="pageSegment" style="--idx:3;">
                    <div class="pageSegment" style="--idx:4;">
                        <div class="pageSegment" style="--idx:5;">
                            <div class="pageSegment" style="--idx:6;">
                                <div class="pageSegment" style="--idx:7;">
                                    <div class="pageSegment" style="--idx:8;"></div>
                                </div>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </div>

    <div class="backSide" style="--imgUrl: {backUrl};">
        <!-- 8 segments -->
        <div class="pageSegment" style="--idx:1;">
            <div class="pageSegment" style="--idx:2;">
                <div class="pageSegment" style="--idx:3;">
                    <div class="pageSegment" style="--idx:4;">
                        <div class="pageSegment" style="--idx:5;">
                            <div class="pageSegment" style="--idx:6;">
                                <div class="pageSegment" style="--idx:7;">
                                    <div class="pageSegment" style="--idx:8;"></div>
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

        transition: transform 500ms ease-out;
    }

    .frontSide,
    .backSide {
        width: 100%;
        height: 100%;
        transform-style: preserve-3d;
        backface-visibility: hidden;
    }

    .backSide {
        transform: rotateY(180deg);
    }

    .pageSegment {
        transform-style: preserve-3d;

        transition: transform 500ms ease-out;
        background-size: 100% 100%;
        background-position-x: calc(-1 * var(--idx) * 100% / 8);
    }

    .flip > * > .pageSegment {
        animation: segmentAnimation 500ms 1 ease-out;
    }

    .flip.reverse > * > .pageSegment {
        animation: reverseSegmentAnimation;
    }

    .pageRoot.flip {
        animation: pageAnimation 500ms ease-out 1;
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
            transform: rotateY(180deg);
        }
    }
</style>
