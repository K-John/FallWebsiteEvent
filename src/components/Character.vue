<template>
    <div class="character tooltip" :style="cssVars">
        <span class="tooltiptext">{{ character.name }}</span>
        <div v-if="chasingCorn" class="showtooltiptext">!!</div>
        <div v-else-if="eating" class="showtooltiptext">:)</div>
        <div v-else-if="crying" class="showtooltiptext">:'(</div>
        <div :class="{ wobble: walking && !flipped, 'wobble-flipped': walking && flipped, flip: flipped, eat: eating && !flipped, 'eat-flipped': eating && flipped }" ref="svg">
            <img :src="character.image" class="head" :alt="character.name" />
            <svg class="rider" viewBox="0 0 500 500" xmlns="http://www.w3.org/2000/svg">
                <rect x="373.339" y="166.33" width="112.529" height="43.187" style="fill: rgb(77, 76, 76);"
                    transform="matrix(0.661254, 0.750162, -0.750162, 0.661254, 69.38475, -4.447453)" />
                <rect x="189.255" y="183.591" width="99.003" height="203.34" style="fill: rgb(80, 109, 152);" rx="6.435"
                    ry="6.435" transform="matrix(0.713674, -0.700478, 0.700478, 0.713674, -114.554504, 245.956436)" />
                <rect x="143.336" y="156.409" width="74.982" height="173.234" style="fill: rgb(80, 109, 152);"
                    rx="6.435" ry="6.435"
                    transform="matrix(0.700478, 0.713674, -0.713674, 0.700478, 319.88858, 55.462639)" />
                <rect x="152.179" y="0.653" width="116.194" height="251.36" style="fill: rgb(113,113,113)" rx="6.971"
                    ry="6.971" />
                <rect x="152.179" y="0.653" width="116.194" height="251.36"
                    :style="'fill: ' + stringToColor(character.username, true)" rx="6.971" ry="6.971" />
                <rect x="159.395" y="42.852" width="125.871" height="46.836"
                    :style="'fill: ' + stringToColor(character.username, false)"
                    transform="matrix(0.694905, 0.719102, -0.719102, 0.694905, 138.547516, -116.799286)" />
                <rect x="130.06" y="42.852" width="102.705" height="46.836"
                    :style="'fill: ' + stringToColor(character.username, false)"
                    transform="matrix(0.934305, 0.356475, -0.356475, 0.934305, 182.700089, 20.538328)" />
                <path style="stroke: rgb(0, 0, 0); fill: none; stroke-width: 5px;"
                    d="M 380.559 164.139 L 393.016 293.108 L 448.708 324.617" />
            </svg>
            <img src="../assets/turkey.svg" class="turkey" alt="Turkey" />
        </div>
    </div>
</template>
<script>

export default {
    name: 'character',
    props: ['character', 'corn', 'cornLocation'],
    data() {
        return {
            currentPos: 0,
            goalPos: 0,
            interval: undefined,
            flipped: false,
            walking: false,
            eating: false,
            crying: false,
            chasingCorn: false
        }
    },
    computed: {
        cssVars() {
            return {
                '--left': this.currentPos + 'px'
            }
        },
        maxWidth() {
            return document.body.clientWidth - 100;
        }
    },
    methods: {
        startNewGoal() {
            // Chase corn if it's available
            if (this.corn) {
                this.chasingCorn = true;
                this.interval = setInterval(this.chaseCorn, 5);
                return;
            }

            // Calculate new walk distance
            let walkDistance = Math.floor(Math.random() * 200);

            // 50% chance of going backwards
            if (Math.random() < 0.5) {
                walkDistance = walkDistance * -1;
            }
            // Don't allow out of bounds
            walkDistance = (this.currentPos + walkDistance > this.maxWidth) ? (this.maxWidth - this.currentPos - 130) : (this.currentPos + walkDistance < 0) ? 0 : walkDistance;

            // Set new goal with interval
            this.goalPos = this.currentPos + walkDistance;
            this.interval = setInterval(this.chaseGoal, 10);

        },
        chaseCorn() {
            if (!this.corn) { this.cry(); return; }

            let goal = (this.cornLocation > this.currentPos) ? this.cornLocation - 80 : this.cornLocation + 40;

            if (this.currentPos == goal) {
                if (this.cornLocation > this.currentPos && this.flipped) {
                    this.flipped = false;
                }
                if (this.cornLocation < this.currentPos && !this.flipped) {
                    this.flipped = true;
                }
                this.eatCorn();
                return;
            }

            this.walkToPos(goal);
        },
        eatCorn() {
            this.$emit('eaten', this.character);
            this.stopWalking();
            this.eating = true;

            setTimeout(() => {
                this.eating = false;
                this.startNewGoal();
            }, 2000);
        },
        cry() {
            this.stopWalking();
            this.crying = true;

            setTimeout(() => {
                this.crying = false;
                this.startNewGoal();
            }, 1000);
        },
        chaseGoal() {
            if (this.currentPos == this.goalPos) {
                this.finishGoal();
                return;
            }
            this.walkToPos(this.goalPos);
        },
        walkToPos(pos) {
            if (!this.walking) {
                this.walking = true;
            }
            if (this.currentPos > pos) {
                if (!this.flipped) {
                    this.flipped = true;
                }
                this.currentPos--;
            } else {
                if (this.flipped) {
                    this.flipped = false;
                }
                this.currentPos++;
            }
        },
        finishGoal() {
            this.stopWalking();

            let randomWait = Math.floor(Math.random() * 5000);
            setTimeout(() => { this.startNewGoal(); }, randomWait);
        },
        stopWalking() {
            this.chasingCorn = false;
            this.walking = false;
            clearInterval(this.interval);
        },
        stringToColor(str, lighter) {
            var hash = 0;
            for (var i = 0; i < str.length; i++) {
                hash = str.charCodeAt(i) + ((hash << 5) - hash);
            }
            var color = '#';
            for (var i = 0; i < 3; i++) {
                var value = (hash >> (i * 8)) & 0xFF;
                color += ('00' + value.toString(16)).substr(-2);
            }
            if (lighter) {
                color += 90;
            }
            return color;
        }
    },
    mounted() {
        this.currentPos = Math.floor(Math.random() * this.maxWidth);

        this.startNewGoal();
    },
    destroyed() {
        clearInterval(this.interval);
    }
}
</script>
<style>
.character {
    position: absolute;
    z-index: 1;
    bottom: 15px;
    left: var(--left);
    width: 75px;
}

.turkey {
    margin-top: -82px;
}

.rider {
    position: relative;
    margin-top: -8px;
}

.eat {
    animation: eat 2s 1;
}

.eat-flipped {
    animation: eat-flipped 2s 1;
}

.flip {
    -webkit-transform: scaleX(-1);
    transform: scaleX(-1);
}

.flip .tooltiptext {
    transform: scaleX(1);
}

.wobble {
    animation: wobble .75s infinite;
}

.wobble-flipped {
    animation: wobble-flipped .75s infinite;
}

@keyframes eat {
    0%, 100% {
        transform: rotate(0deg);
        transform-origin: bottom;
    }

    17% {
        transform: rotate(45deg);
        transform-origin: bottom;
    }

    33% {
        transform: rotate(35deg);
        transform-origin: bottom;
    }

    50% {
        transform: rotate(45deg);
        transform-origin: bottom;
    }

    67% {
        transform: rotate(35deg);
        transform-origin: bottom;
    }

    84% {
        transform: rotate(45deg);
        transform-origin: bottom;
    }
}

@keyframes eat-flipped {
    0%, 100% {
        transform: rotate(0deg) scaleX(-1);
        transform-origin: bottom;
    }

    17% {
        transform: rotate(-45deg) scaleX(-1);
        transform-origin: bottom;
    }

    33% {
        transform: rotate(-35deg) scaleX(-1);
        transform-origin: bottom;
    }

    50% {
        transform: rotate(-45deg) scaleX(-1);
        transform-origin: bottom;
    }

    67% {
        transform: rotate(-35deg) scaleX(-1);
        transform-origin: bottom;
    }

    84% {
        transform: rotate(-45deg) scaleX(-1);
        transform-origin: bottom;
    }
}

@keyframes wobble {
    0% {
        transform: rotate(0deg);
    }

    25% {
        transform: rotate(5deg);
    }

    50% {
        transform: rotate(0deg);
    }

    75% {
        transform: rotate(-5deg);
    }

    100% {
        transform: rotate(0deg);
    }
}

@keyframes wobble-flipped {
    0% {
        transform: rotate(0deg) scaleX(-1);
    }

    25% {
        transform: rotate(5deg) scaleX(-1);
    }

    50% {
        transform: rotate(0deg) scaleX(-1);
    }

    75% {
        transform: rotate(-5deg) scaleX(-1);
    }

    100% {
        transform: rotate(0deg) scaleX(-1);
    }
}
</style>