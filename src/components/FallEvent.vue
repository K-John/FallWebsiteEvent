<template>
    <div>
        <button class="static-button tooltip" @click="enabled = !enabled">
            <span class="tooltiptext">Toggle Event</span>
            <img src="../assets/pumpkin.svg" alt="Pumpkin" />
        </button>
        <div class="event-container" v-if="enabled">

            <div class="log">
                <ul>
                    <li v-for="character in log.slice(-5)">
                        <img :src="character.image" class="head" />
                        &nbsp;{{ character.name }} ate the corn
                    </li>
                </ul>
            </div>

            <Character v-for="character in characters" :key="character.username" :character="character" :corn="corn"
                :corn-location="cornLocation" @eaten="cornEaten"></Character>

            <img src="../assets/cornstalk.svg" v-for="pos in cornstalkPos" :key="pos" :style="'left: ' + pos + 'px;'" class="cornstalk" alt="Cornstalk">

            <img src="../assets/tree.svg" v-for="pos in treePos" :key="pos" :style="'left: ' + pos + 'px;'" class="tree" alt="Tree" />

            <button v-if="cornAvailable" @click="throwCorn" class="corn-button">Throw Corn</button>
            <img v-if="cornLocation !== -1" src="../assets/corn.svg" :style="'left: ' + cornLocation + 'px'"
                class="corn" alt="Corn" />

            <!-- Ground -->
            <div class="custom-shape-divider-bottom-1664148032">
                <svg data-name="Layer 1" xmlns="http://www.w3.org/2000/svg" viewBox="0 0 1200 120"
                    preserveAspectRatio="none">
                    <path
                        d="M321.39,56.44c58-10.79,114.16-30.13,172-41.86,82.39-16.72,168.19-17.73,250.45-.39C823.78,31,906.67,72,985.66,92.83c70.05,18.48,146.53,26.09,214.34,3V0H0V27.35A600.21,600.21,0,0,0,321.39,56.44Z"
                        class="shape-fill"></path>
                </svg>
            </div>
            <div class="ground"></div>

            <a href="https://github.com/K-John/FallWebsiteEvent" target="_blank" class="source-link"><img src="../assets/github.svg" />Open Source by Kendall Johnson</a>

        </div>
    </div>
</template>
<script>
import Character from "./Character.vue";

export default {
    name: 'fall-event',
    props: ['characters'],
    data() {
        return {
            enabled: false,
            corn: false,
            cornLocation: -1,
            log: []
        };
    },
    computed: {
        cornAvailable() {
            return (!this.corn && this.cornLocation == -1);
        },
        maxWidth() {
            return document.body.clientWidth - 100;
        },
        halfWidth() {
            return this.maxWidth / 2;
        },
        cornstalkPos() {
            let count = 10;
            let gap = this.halfWidth / count;

            return [...Array(count).keys()].map(x => x * gap);
        },
        treePos() {
            let count = 5;
            let gap = this.halfWidth / count;

            return [...Array(count).keys()].map(x => (this.maxWidth - 200 - x * gap));
        }
    },
    methods: {
        throwCorn() {
            if (this.corn) return;

            let twoThirdsHalfWidth = this.halfWidth * (2 / 3);

            let negative = (Math.random() < 0.5) ? -1 : 1;

            this.cornLocation = this.halfWidth + (Math.floor(Math.random() * twoThirdsHalfWidth) * negative);
            setTimeout(() => { this.corn = true; }, 800);
        },
        cornEaten(character) {
            this.log.push(character);
            this.corn = false;

            setTimeout(() => { this.cornLocation = -1; }, 2000);
        }
    },
    components: { Character }
}
</script>

<style>
.log {
    display: flex;
    position: absolute;
    bottom: 150px;
    z-index: 2;
}

.log ul {
    list-style-type: none;
}

.log ul li {
    display: flex;
    align-items: center;
    color: #000;
}

.head {
    width: 35px;
    border-radius: 50%;
    position: relative;
    z-index: 1;
    margin-left: -12px;
}

.corn {
    position: absolute;
    bottom: 5px;
    z-index: 1;
    width: 35px;
    transform: rotate(75deg);
    animation: cornthrow 1s 1 ease-in-out;
}

.cornstalk {
    position: absolute;
    bottom: 19px;
    height: 100px;
    left: 0px;
}

.tree {
    position: absolute;
    bottom: 19px;
    height: 200px;
    left: 0px;
}

@keyframes cornthrow {
    0% {
        bottom: -50px;
    }

    75% {
        bottom: 100px;
    }

    100% {
        bottom: 5px;
    }
}

.corn-button {
    background: #eade26;
    color: #000;
    font-weight: bold;
    padding: 10px 15px;
    border-radius: 3px;
    border: none;
    cursor: pointer;
    position: absolute;
    bottom: 175px;
    z-index: 2;
}

@media only screen and (min-width: 1000px) {
    .static-button {
        width: 100px;
        position: absolute;
        right: 30px;
        bottom: 30px;
        background: none;
        border: none;
        cursor: pointer;
        z-index: 2;
    }
}

@media only screen and (max-width: 999px) {
    .static-button {
        display: none;
    }
}

.event-container {
    position: absolute;
    bottom: 0;
    right: 0;
    width: 100vw;
    height: 100vh;
    overflow: hidden;
}

.ground {
    background: #534335;
    height: 20px;
    bottom: 0;
    position: absolute;
    width: 100%;
}

.custom-shape-divider-bottom-1664148032 {
    width: 100%;
    position: absolute;
    bottom: 19px;
    transform: rotate(180deg);
}

.custom-shape-divider-bottom-1664148032 svg {
    position: relative;
    display: block;
    width: calc(100% + 1.3px);
    height: 16px;
}

.custom-shape-divider-bottom-1664148032 .shape-fill {
    fill: #534335;
}

/* Tooltip text */
.tooltip .tooltiptext {
    visibility: hidden;
    background-color: black;
    color: #fff;
    text-align: center;
    padding: 5px 10px;
    border-radius: 6px;
    white-space: nowrap;
}

.tooltip .showtooltiptext {
    background-color: black;
    color: #fff;
    text-align: center;
    padding: 5px 10px;
    border-radius: 6px;
    white-space: nowrap;
    width: fit-content;
    margin: 0 auto;
}

/* Show the tooltip text when you mouse over the tooltip container */
.tooltip:hover .tooltiptext {
    visibility: visible;
}

.source-link {
    position: absolute;
    right: 5px;
    bottom: 0;
    display: flex;
    align-items: center;
    font-size: 12px;
}

.source-link img {
    width: 20px;
    margin-right: 5px;
}
</style>