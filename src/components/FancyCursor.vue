<template>
    <div class="cursor-a" :class="cursorA"></div>
    <div class="cursor-b" :class="cursorB"></div>
</template>

<script setup lang="ts">

import { gsap } from "gsap";
import { ref, onMounted } from "vue";

interface props {
    theme?: string;
    trigger?: string;
}

const props = withDefaults(defineProps<props>(), {
    theme: "vlbn",
    trigger: "a",
});

const cursorA = ref();
const cursorB = ref();

if (props.theme == "vlbn") {
    cursorA.value = "blue"
    cursorB.value = "green"
}

if (props.theme == "ufo") {
    cursorA.value = "alien"
    cursorB.value = "ship"
}

let fancyTl = gsap.timeline({ paused: true });

const cursorIn = () => fancyTl.play();
const cursorOut = () => fancyTl.reverse();

const cursorAttach = ($event: { clientX: any; clientY: any; }) => {
    gsap.set(".cursor-a", {
        x: $event.clientX,
        y: $event.clientY,
    });
    gsap.set(".cursor-b", {
        x: $event.clientX,
        y: $event.clientY,
    });
    gsap.to(".cursor-a", {
        duration: 0.5,
        autoAlpha: 1,
        x: $event.clientX,
        y: $event.clientY,
    });
    gsap.to(".cursor-b", {
        duration: 0.9,
        autoAlpha: 1,
        x: $event.clientX,
        y: $event.clientY,
    });
}

onMounted(() => {

    window.addEventListener("mousemove", cursorAttach);

    let x = document.querySelectorAll(props.trigger);

    for (let i = 0; i < x.length; i++) {
        x[i].addEventListener("mouseover", cursorIn);
        x[i].addEventListener("mouseout", cursorOut);
        x[i].addEventListener("click", cursorOut);
        x[i].addEventListener("mousedown", cursorOut);
    }

    fancyTl = gsap
        .timeline()
        .to(".cursor-a", {
            duration: 0.1,
            scale: 0,
        })
        .to(".cursor-b", {
            duration: 0,
            zIndex: "1",
        }, ">")
        .to(".cursor-b", {
            scale: 1.9,
            duration: 0.4,
            ease: "bounce",
        }, ">")
        .reverse();

})

</script>

<style lang="scss" scoped>

// fancy colors
$green: #42b983;
$blue: #273849;
$paper: #f6f6f6;
$grey: #d4d4d4;

// cursor pointer
.cursor {
    &-a {
        opacity: 0;
        position: fixed;
        width: 10px;
        height: 10px;
        left: -5px;
        top: -5px;
        border-radius: 100%;
        z-index: 1;
        pointer-events: none;
    }
    &-b {
        opacity: 0;
        position: fixed;
        width: 30px;
        height: 30px;
        left: -15px;
        top: -15px;
        border-radius: 100%;
        z-index: 0;
        pointer-events: none;
        mix-blend-mode: multiply;
    }
}

// themes
.green {
    background-color: $green;
}
.blue {
    background-color: $blue;
}
.alien {
    background-color: crimson;
}
.ship {
    background-color: palevioletred;
}
</style>