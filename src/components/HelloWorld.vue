<template>
  <div id="bjsWrap">
    <canvas id="bjsCanvas"></canvas>
  </div>
</template>

<script lang="ts" setup>

import {onMounted, onUnmounted} from "vue";

import * as BABYLON from "babylonjs";
import {Engine, MeshBuilder, PBRMaterial, Scene} from "@babylonjs/core";
import {LottieTexture} from "babylonjs-lottie";
import "@babylonjs/inspector"

async function createScene(scene: Scene) {
  scene.debugLayer.show({embedMode: true});
  scene.createDefaultEnvironment({createSkybox: false, createGround: false});

  let idx: number = 0;
  let filenames: string[] = [
    "/LottieFiles/spacehenry.json",
    "/LottieFiles/Aniki Hamster.json",
    "/LottieFiles/balloons.json",
    "/LottieFiles/car_character.json",
    "/LottieFiles/fun_work_break.json",
    "/LottieFiles/gramaphone.json",
    "/LottieFiles/ramenbowl.json",
    "/LottieFiles/rocking_horse.json",
    "/LottieFiles/tennis_isometric.json",
    "/LottieFiles/train.json"
  ];

  //let lottieTexture = await LottieTexture.ParseFromUrlAsync("lottie", filenames[idx], scene, {
  //  autoPlay: true,
  //  useAnimeSize: false
  //});
  //let box: BABYLON.Mesh = BABYLON.MeshBuilder.CreateBox("box");
  //let mat = new PBRMaterial("pbr", scene);
  let mat: PBRMaterial = new PBRMaterial("pbr", scene);
  mat.unlit = true;

  let box = MeshBuilder.CreateBox("box");
  box.material = mat;
  
  let lottieTexture = LottieTexture.ParseFromUrlAsync("lottie", filenames[idx], scene, {autoPlay: true,useAnimeSize: false}).then((result: LottieTexture) => {
    mat.albedoTexture = result;
  });

  const timerId = setInterval(() => {
    idx = (idx + 1) % filenames.length;
    let lottieTexture = LottieTexture.ParseFromUrlAsync("lottie", filenames[idx], scene, {autoPlay: true, useAnimeSize: false}).then((result: LottieTexture) => {
      mat.albedoTexture = result;
    });
  }, 6000);
   
  scene.createDefaultCameraOrLight(true, true, true)
}

onMounted(() => {
  let engine = new Engine(document.querySelector("#bjsCanvas") as HTMLCanvasElement)
  let scene = new Scene(engine)
    scene.createDefaultCamera()

    createScene(scene)

    engine.runRenderLoop(() => {
        scene.render()
    })

    let resize = () => engine.resize()
    window.addEventListener("resize", resize)
    onUnmounted(() => {
        scene.dispose()
        engine.dispose()
        window.removeEventListener("resize", resize)
    })
})

</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
#bjsCanvas {
  width: 100%;
  height: 100%;
  display: block;
}

#bjsWrap {
  width: 100vw;
  height: 100vh;
  padding: 0;
  margin: 0;
}
</style>
