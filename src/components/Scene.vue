<template>
  <div class="scene">
    <div id="three-scene-canvas"></div>
  </div>
</template>

<script>
import * as THREE from 'three'
import { OrbitControls } from 'three/examples/jsm/controls/OrbitControls'
import * as GltfLoader from 'three-gltf-loader'

export default {
  name: 'Scene',
  data () {
    return {
      sceneCanvas: null,
      scene: null,
      camera: null,
      renderer: null,
      controls: null
    }
  },
  mounted () {
    /* **************
       BASIC SETUP
    ************** */

    this.sceneCanvas = document.getElementById('three-scene-canvas')
    this.scene = new THREE.Scene()
    this.camera = new THREE.PerspectiveCamera(
      75,
      this.sceneCanvas.getBoundingClientRect().width / this.sceneCanvas.getBoundingClientRect().height,
      0.1,
      3000
    )
    this.camera.position.set(300, 100, 300)
    
    this.renderer = new THREE.WebGLRenderer({
      antialias: true,
      powerPreference: "high-performance"
    })
    this.renderer.outputEncoding = THREE.sRGBEncoding

    this.controls = new OrbitControls(this.camera, this.renderer.domElement)
    this.controls.addEventListener('change', this.animateThreeJs )

    this.renderer.setSize(this.sceneCanvas.offsetWidth, this.sceneCanvas.offsetHeight)
    this.renderer.setClearColor("#000000")
    this.renderer.shadowMap.enabled = true
    this.renderer.shadowMap.type = THREE.PCFSoftShadowMap
    this.renderer.shadowMapSoft = true
    this.renderer.shadowMap.autoUpdate = false
    this.renderer.shadowMap.needsUpdate = true


    this.sceneCanvas.append(this.renderer.domElement)
    
    // lighting
    //let ambientLight = new THREE.AmbientLight (0xdaccff, 0.1)
    //this.scene.add(ambientLight)

    let orangeLight = new THREE.SpotLight(0xfc831d, 1, 700, 360 * 2)
    orangeLight.castShadow = true
    orangeLight.position.set(0, 300, 450)
    this.scene.add(orangeLight)

    let purpleLight = new THREE.SpotLight(0xfc1de5, 1, 700, 360 * 2)
    purpleLight.castShadow = true
    purpleLight.position.set(0, 100, -350)
    this.scene.add(purpleLight)

    let blueLight = new THREE.SpotLight(0x1da0fc, 1, 700, 360 * 2)
    blueLight.castShadow = true
    blueLight.position.set(250, 100, 50)
    this.scene.add(blueLight)

    let redLight = new THREE.SpotLight(0xfc421d, 1, 700, 360 * 2)
    redLight.castShadow = true
    redLight.position.set(-250, 100, 50)
    this.scene.add(redLight)

    // Adding a cube
    
    /*let geometryC = new THREE.BoxGeometry(10,10,10)
    let materialC = new THREE.MeshPhysicalMaterial({color: 0x00ff00})
    let cube = new THREE.Mesh(geometryC, materialC)
    cube.position.set(-150, -130, 100)
    cube.castShadow = true
    cube.receiveShadow = true
    this.scene.add(cube)*/

    // The Ground
    let textureLoader = new THREE.TextureLoader()
    textureLoader.load('/textures/rocks.jpg', (texture) => {
      texture.wrapS = THREE.RepeatWrapping
      texture.wrapT = THREE.RepeatWrapping
      texture.repeat.set( 20, 20 )
      texture.anisotropy = this.renderer.getMaxAnisotropy()

      let geometry = new THREE.PlaneGeometry( 2000, 2000 )
      let material = new THREE.MeshPhysicalMaterial({
        color: 0xffffff,
        side: THREE.DoubleSide,
        map: texture
      })
      material.roughness = 0.65
      material.metalness = 0.75

      let plane = new THREE.Mesh( geometry, material )
      plane.rotation.x = 90 * Math.PI/180
      plane.position.y = -140
      plane.receiveShadow = true

      this.scene.add( plane )
    })
    

    // Loading the car object
    let gltfLoader = new GltfLoader()
    gltfLoader.load('/blender-files/car/scene.gltf', (gltf) => {
      let model = gltf.scene.children[0]
      model.traverse((obj) => {
        if (obj.isMesh) {
          obj.castShadow = true
          obj.receiveShadow = true
        }
      })
      this.scene.add(model)
      this.animateThreeJs()
    })

    this.animateThreeJs()
  },
  methods: {
    animateThreeJs () {
      this.renderer.render(this.scene, this.camera)
      this.renderer.shadowMap.needsUpdate = true
    }
  }
}
</script>
