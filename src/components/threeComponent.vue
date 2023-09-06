<template>
  <div>
    <div id="container" class="absolute top-0 bottom-0 left-0 right-0 ">
    </div>
    <div class="my-4"/>
    <div class="relative z-100">
      <h1 class="text-xs text-white ml-4">Texture</h1>
      <div class="flex flex-wrap w-[250px] ml-4  gap-2">
        <div v-for="(texture, i) in myTexture" @click="updateTexture(texture)" :key="i">
            <img :src="texture" class="w-[75px] aspect-square" alt="">
        </div>

      </div>

      <div class="my-4"/>

      <h1 class="text-xs text-white ml-4">Font</h1>
      <div class="flex flex-wrap flex-col w-[250px] ml-4 gap-2">
        <div v-for="(font, i) in fontOptions" class="lg:hover:bg-white/20" @click.stop="updateFont(font)" :key="i">
            <span class="text-xs text-white  lg:cursor-pointer">{{ font }}</span>
        </div>

      </div>
    </div>
  </div>



</template>

<script setup>

import * as THREE from 'three';
import { onMounted, ref } from 'vue';
import { OrbitControls } from 'three/addons/controls/OrbitControls.js';
import { TextGeometry } from 'three/addons/geometries/TextGeometry.js';
import { FontLoader } from 'three/addons/loaders/FontLoader.js';
import { GUI } from 'dat.gui';


let geometry = null;
let controls = null;
let renderer = null;
let scene = null;
let camera = null;
let material = null;
let texture = null;
let currentFont = 'Dagtton_Regular.json';

const myTexture = ref([
  'pattern.jpg',
  'shiny-gold-texture-paper-metal-golden-foil_88211-1971.avif',
  'silver-metal-background-1_78370-324.avif'
])

const fontOptions = ref([
  'Dagtton_Regular.json',
  'MoglanDemo_Regular.json'
  ]);


const size = ref({
  width : window.innerWidth,
  height : window.innerHeight
})

let mesh = null;

const guiProperties = {
  size: 16,
  height: 2,
  curveSegments: 12,
  bevelEnabled: true,
  bevelThickness: 1,
  bevelSize: 1,
  bevelOffset: 0,
  bevelSegments: 1
};

const materialOptions = {

  color : 0xe8d203,
  metalness : 1,
  roughness : 0.4,
  map : null
}

const textInput = ref('Helloworld');

onMounted(() =>{

    scene = new THREE.Scene();

    const loader = new FontLoader();
    loader.load(currentFont, function (font) {
        geometry = new TextGeometry(textInput.value, {

            font : font,
            size : guiProperties.size,
            height : guiProperties.height,
            curveSegments: guiProperties.curveSegments,
            bevelEnabled: guiProperties.bevelEnabled,
            bevelThickness: guiProperties.bevelThickness,
            bevelSize: guiProperties.bevelSize,
            bevelOffset: guiProperties.bevelOffset,
            bevelSegments: guiProperties.bevelSegments

        });

        geometry.computeBoundingBox();
        const textWidth = geometry.boundingBox.max.x - geometry.boundingBox.min.x;
        const textHeight = geometry.boundingBox.max.y - geometry.boundingBox.min.y;



        material = new THREE.MeshStandardMaterial(materialOptions)

        mesh = new THREE.Mesh(geometry, material);
        mesh.position.set(-textWidth / 2, -textHeight / 2, 0);
        scene.add(mesh);

    //control


    //light

    const light = new THREE.PointLight(0xffffff, 30, 100000);
    light.position.set(4, 4, 3);
    scene.add(light);

    const light2 = new THREE.PointLight(0xffffff, 30, 100000);
    light2.position.set(-4, 4, 1);
    scene.add(light2);

    const light3 = new THREE.PointLight(0xffffff, 30, 100000);
    light3.position.set(-16, -4, 1);
    scene.add(light3);

    const light4 = new THREE.PointLight(0xffffff, 30, 100000);
    light4.position.set(-textWidth/2, 10, 1);
    scene.add(light4);

    const ambientLight = new THREE.AmbientLight(0xffffff, 5); // Soft white light
    scene.add(ambientLight);

    const directionalLight = new THREE.DirectionalLight( 0xffffff, 5);
    directionalLight.position.set(5, 20, 30);
    scene.add( directionalLight );

    const directionalLight2 = new THREE.DirectionalLight( 0xffffff, 5);
    directionalLight2.position.set(-5, -20, -30);
    scene.add( directionalLight2 );

    const lightHEi = new THREE.HemisphereLight( 0xffffbb, 0xffffff, 8 );
    scene.add( lightHEi );

    //camera

    camera = new THREE.PerspectiveCamera(45, size.value.width / size.value.height, 0.1, 1000);

    camera.position.z = 150;
    camera.lookAt(0, 0, 0);

    scene.add(camera);

    const containerELE = document.querySelector('#container');
    renderer = new THREE.WebGLRenderer();
    renderer.setPixelRatio(2);
    renderer.setSize(size.value.width, size.value.height);

    containerELE.appendChild(renderer.domElement);


    controls = new OrbitControls( camera, renderer.domElement );
    controls.enableDamping = true;
    controls.enablePan = false;
    controls.enableZoom = true;
    controls.autoRotate = false;
    controls.autoRotateSpeed = 5;

    initGUI();


    animate();




    })
   

})

const animate = () => {

controls.update();
renderer.render(scene, camera);
requestAnimationFrame(animate);

}

const updateTextGeometry = () => {

  if (geometry) {
    scene.remove(mesh);
    geometry.dispose(); // Dispose of the previous geometry to release resources
  }
  const loader = new FontLoader();
  loader.load(currentFont, function (font) {
    geometry = new TextGeometry(textInput.value, {
        font : font,
        size : guiProperties.size,
        height : guiProperties.height,
        curveSegments: guiProperties.curveSegments,
        bevelEnabled: guiProperties.bevelEnabled,
        bevelThickness: guiProperties.bevelThickness,
        bevelSize: guiProperties.bevelSize,
        bevelOffset: guiProperties.bevelOffset,
        bevelSegments: guiProperties.bevelSegments
    });

    geometry.computeBoundingBox();
    const textWidth = geometry.boundingBox.max.x - geometry.boundingBox.min.x;
    const textHeight = geometry.boundingBox.max.y - geometry.boundingBox.min.y;

    const material = new THREE.MeshStandardMaterial(materialOptions);

    mesh = new THREE.Mesh(geometry, material);
    mesh.position.set(-textWidth / 2, -textHeight / 2, 0);
    scene.add(mesh);

    // Render the updated scene
    renderer.render(scene, camera);
  });
};


const initGUI = () => {
  const gui = new GUI();

  // Add controls for text-related parameters
  const textFolder = gui.addFolder('Text Settings');
  textFolder.add(textInput, 'value').name('Text').onChange(updateTextGeometry);
  textFolder.add(guiProperties, 'size', 1, 100).name('Size').onChange(updateTextGeometry);
  textFolder.add(guiProperties, 'height', 1, 10).name('Height').onChange(updateTextGeometry);
  textFolder.add(guiProperties, 'curveSegments', 1, 50).name('Curve Segments').onChange(updateTextGeometry);
  textFolder.add(guiProperties, 'bevelEnabled').name('Bevel Enabled').onChange(updateTextGeometry);
  textFolder.add(guiProperties, 'bevelThickness', 0.1, 5).name('Bevel Thickness').onChange(updateTextGeometry);
  textFolder.add(guiProperties, 'bevelSize', 0.1, 5).name('Bevel Size').onChange(updateTextGeometry);
  textFolder.add(guiProperties, 'bevelOffset', -2, 2).name('Bevel Offset').onChange(updateTextGeometry);
  textFolder.add(guiProperties  , 'bevelSegments', 0, 5).name('Bevel Segments').onChange(updateTextGeometry);

  // Add controls for camera settings (if needed)
  // const cameraFolder = gui.addFolder('Camera Settings');
  // ...

  // Add controls for lighting settings (if needed)
  // const lightingFolder = gui.addFolder('Lighting Settings');
  // ...

  const meshStandardMaterialFolder = gui.addFolder('Material properties');

  meshStandardMaterialFolder.addColor(materialOptions, 'color').onChange(updateTextGeometry);
  meshStandardMaterialFolder.add(materialOptions, 'metalness').onChange(updateTextGeometry);
  meshStandardMaterialFolder.add(materialOptions, 'roughness').onChange(updateTextGeometry);



  // You can customize and add more controls as needed
  meshStandardMaterialFolder.open();
  textFolder.open(); // Open the text-related settings folder by default
  
};


const updateTexture = (url) => {
  const textureLoader = new THREE.TextureLoader();

  texture = textureLoader.load(url);
  materialOptions.color = 0xffffff;
  materialOptions.map = texture;
  material.needsUpdate = true;
  updateTextGeometry();
}


const updateFont = (font) => {
  console.log(font);
  currentFont = font;
  material.needsUpdate = true;
  updateTextGeometry();
}




</script>

<style>
</style>