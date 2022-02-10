<template>
    <div class="cursor-a" :class="cursorA"></div>
    <div class="cursor-b" :class="cursorB"></div>
</template>

<script setup>

import { ref, onMounted, onBeforeUnmount } from "vue";

import { gsap } from "gsap";

const props = defineProps({
    trigger: { type: String, default: "a" },
});

const cursorA = ref("blue");
const cursorB = ref("green");

let fancyTl = gsap.timeline({ paused: true });

const cursorIn = () => fancyTl.play();
const cursorOut = () => fancyTl.reverse();

const cursorAttach = ($event) => {
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

window.addEventListener("mousemove", cursorAttach);

onMounted(() => {

    let iTrigger = document.querySelectorAll(props.trigger);

    for (let i = 0; i < iTrigger.length; i++) {
        iTrigger[i].addEventListener("mouseover", cursorIn);
        iTrigger[i].addEventListener("mouseout", cursorOut);
        iTrigger[i].addEventListener("click", cursorOut);
        iTrigger[i].addEventListener("mousedown", cursorOut);
    }

    gsap.set(iTrigger, {
        zIndex: "1",
        position: "relative"
    });

    fancyTl = gsap
        .timeline()
        .to(".cursor-a", {
            duration: 0.1,
            scale: 0,
        })
        .to(".cursor-b", {
            duration: 0,
            zIndex: "2",
        }, ">")
        .to(".cursor-b", {
            scale: 1.9,
            duration: 0.4,
            ease: "bounce",
        }, ">")
        .reverse();

})

onBeforeUnmount(() => {
    window.removeEventListener("mousemove", cursorAttach);
    gsap.to(".cursor-b", {
        duration: 1.3,
        scale: 100,
        autoAlpha: 0,
    });
})

</script>

<style lang="scss">
// fancy colors
$green: #42b983;
$blue: #273849;

.green {
    background-color: $green;
}
.blue {
    background-color: $blue;
}

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
        z-index: 2;
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
        z-index: 1;
        pointer-events: none;
        mix-blend-mode: multiply;
    }
}
</style>