<template>
    <div id="main_container" ref="main_container">
        <header>
            <h1>The Specialist in Failure</h1>
        </header>
        <main>
            <section class="player">
                <div class="controls">
                    <button class="toggle" @click="toggle" type="button">
                        {{ audio.title }}
                    </button>
                </div>
            </section>
        </main>
        <div>
            <img
                id="plane_image"
                ref="plane_image"
                class="plane"
                :src="getImage(currentPlane.direction)"
                v-show="show"
            />
        </div>
    </div>
</template>

<script>
import gsap from "gsap";

function randomInteger(min, max) {
    return Math.round(Math.random() * (max - min) + min);
}

function getDirectionCooredinates(window, direction) {
    let offset = 500;
    var finalPosition = { x: 0, y: 0 };
    var initialPosition = { x: 0, y: 0 };
    if (direction == "left_to_right") {
        initialPosition.x = -offset;
        finalPosition.x = window.clientWidth;
    }
    if (direction == "right_to_left") {
        finalPosition.x = -offset;
        initialPosition.x = window.clientWidth;
    }
    initialPosition.y = randomInteger(40, window.clientHeight - 60);
    return { initialPosition, finalPosition };
}

export default {
    name: "App",
    components: {},
    data() {
        return {
            current: {},
            isPlaying: false,
            audio: {
                title: "Tap to Chant!",
                src: require("./assets/audio.mp3"),
            },
            player: new Audio(),
            directions: ["left_to_right", "right_to_left"],
            currentPlane: {
                direction: "left_to_right",
            },
            show: false,
        };
    },
    methods: {
        play(audio) {
            if (typeof audio.src != "undefined") {
                this.current = audio;
                this.player.src = this.current.src;
            }
            this.player.play();
            this.isPlaying = true;
            this.player.onended = function () {
                this.isPlaying = false;
            };
        },
        stop() {
            this.player.pause();
            this.player.currentTime = 0;
            this.isPlaying = false;
        },
        toggle() {
            if (!this.isPlaying) {
                this.play(this.audio);
            } else {
                this.stop();
            }
        },
        hide() {},
        animatePlane(direction) {
            var timeline = gsap.timeline();
            var container = this.$refs["main_container"];
            var planeObject = this.$refs["plane_image"];
            var { initialPosition, finalPosition } = getDirectionCooredinates(
                container,
                direction
            );
            console.log({ initialPosition, finalPosition });
            timeline.set(planeObject, initialPosition);
            timeline.to(planeObject, {
                duration: 10,
                x: finalPosition.x,
            });
            this.show = true;
            timeline.play();
        },
        getImage(path) {
            return require("./assets/banners/banner_" + path + ".png");
        },
    },
    created() {
        this.current = this.audio;
        this.player.src = this.current.src;
        this.direction = "left_to_right";
        this.counter = 0;
    },
    mounted() {
        var self = this;
        this.$nextTick(function () {
            window.setInterval(() => {
                self.currentPlane.direction = self.directions[self.counter % 2];
                self.counter += 1;
                self.animatePlane(self.currentPlane.direction);
            }, randomInteger(11000, 15000));
        });
    },
};
</script>

<style>
* {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
}
body {
    font-family: sans-serif;
    background-image: url("./assets/OldTrafford.jpg");
    background-size: cover;
    background-repeat: no-repeat;
    overflow: hidden;
}
header {
    display: flex;
    justify-content: center;
    align-items: center;
    padding: 15px;
    background-color: #d41313;
    color: #ffffff;
}

main {
    width: 100%;
    max-width: 1080px;
    margin: 0 auto;
    padding: 25px;
}
.overlay {
    z-index: 1;
    height: 100%;
    width: 100%;
    position: relative;
    overflow: auto;
    top: 0px;
    left: 0px;
    background: rgba(0, 0, 0, 0.7); /*can be anything, of course*/
}
.song-title {
    color: #53565a;
    font-size: 32px;
    font-weight: 700;
    text-transform: uppercase;
    text-align: center;
}
.controls {
    display: flex;
    justify-content: center;
    align-items: center;
    padding: 30px 15px;
}
button {
    appearance: none;
    background: none;
    border: none;
    outline: none;
    cursor: pointer;
}
button:hover {
    opacity: 0.8;
}

.slide-fade-enter-active {
    animation: slide-fade 15s linear 10ms infinite;
}
.slide-fade-leave-active {
    animation: slide-fade 0.1s linear;
    opacity: 0;
}

@keyframes slide-fade {
    0% {
        transform: scale(1) translateX(-120vw);
    }
    100% {
        transform: scale(1) translateX(0vw);
    }
}
.toggle {
    font-size: 20px;
    font-weight: 700;
    padding: 15px 25px;
    margin: 0px 15px;
    border-radius: 8px;
    color: #fff;
    background-color: #cc2e5d;
}
.plane {
    position: relative;
    will-change:transform;
}
</style>
