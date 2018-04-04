<template>
    <div class="sceneContainer">
        <i-scene experimental-webgl="true" ref="scene">

            <i-perspective-camera v-once active
                ref="camera"
                far="8000"
                :position="[0, getY(worldHalfWidth, worldHalfDepth), 3000]"
            >
            </i-perspective-camera>

            <i-ambient-light color="#cccccc"></i-ambient-light>

            <!-- TODO -->
            <!--<i-directional-light color="#ffffff" intensity="2"></i-directional-light>-->

            <template v-for="n in 10">
                <p>{{n}}</p>
            </template>

            <template v-for="z in worldDepth">
                <template v-for="x in worldWidth">
                    <i-box has="basic-material" size="100 100 100"
                        :position="[
                            (x-1) * 100 - worldHalfWidth * 100,
                            getY(x-1, z-1) * 100,
                            (z-1) * 100 - worldHalfDepth * 100
                        ]"
                        :color="(z+x)%2 == 0 ? 'red' : 'blue'"
                    >
                    </i-box>
                </template>
            </template>

        </i-scene>
    </div>
</template>

<script>
    /* eslint-disable */
    import {useDefaultNames} from 'infamous/html'
    import Motor from 'infamous/core/Motor'

    import * as THREE from 'three'
    import FirstPersonControls from '../lib/three/FirstPersonControls'
    import ImprovedNoise from '../lib/three/ImprovedNoise'

    useDefaultNames()

    const fogExp2 = true
    const clock = new THREE.Clock()

    export default {

        data: () => ({
            worldWidth: 20,
            worldDepth: 20
        }),

        computed: {
            worldHalfWidth() { return this.worldWidth / 2 },
            worldHalfDepth() { return this.worldDepth / 2 },
            terrainData() { return generateHeight(this.worldWidth, this.worldDepth) },
        },
        mounted () {
            console.log('camera?', this.$refs.camera.threeObject3d)
            this.$refs.camera.threeObject3d.aspect = window.innerWidth / window.innerHeight
            window.camera1 = this.$refs.camera.threeObject3d
            const controls = new FirstPersonControls( this.$refs.camera, void 0, THREE )

            controls.movementSpeed = 1000
            controls.lookSpeed = 0.125
            controls.lookVertical = true
            controls.constrainVertical = true
            controls.verticalMin = 1.1
            controls.verticalMax = 2.2

            const scene = this.$refs.scene.threeObject3d
            //scene.background = new THREE.Color( 0xffffff );
            scene.fog = new THREE.FogExp2( 0xffffff, 0.00015 );

            let dt = 0
            const task = Motor.addRenderTask(() => {
                dt = clock.getDelta()
                controls.update(dt)
                this.$refs.scene._needsToBeRendered()
            })

            setTimeout(() => Motor.removeRenderTask(task), 5000)
        },

        methods: {
            getY(x, z) {
                return (this.terrainData[x + z * this.worldWidth] * 0.2) | 0
            }
        }

    }

    function generateHeight (width, height) {
        const data = []
        const perlin = new ImprovedNoise()
        const size = width * height
        const z = Math.random() * 100
        let quality = 2
        for (let j = 0; j < 4; j++) {
            if (j === 0) for (let i = 0; i < size; i++) data[i] = 0
            for (let i = 0; i < size; i++) {
                let x = i % width
                let y = (i / width) | 0
                data[i] += perlin.noise(x / quality, y / quality, z) * quality
            }
            quality *= 4
        }
        return data
    }
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
    .sceneContainer {
        width: 100%;
        height: 100%;
        background: skyblue;
    }

    i-node {
        background: pink;
    }

    h1, h2 {
        font-weight: normal;
    }

    ul {
        list-style-type: none;
        padding: 0;
    }

    li {
        display: inline-block;
        margin: 0 10px;
    }

    a {
        color: #35495E;
    }
</style>
