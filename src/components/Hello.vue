<template>
    <div class="hello">
        <i-scene experimental-webgl="true">
            <i-box align="0.5 0.5 0" mountPoint="0.5 0.5 0" size="100 100" :rotation.prop="[0, rotation, 0]">
            </i-box>
        </i-scene>
    </div>
</template>

<script>
    import {useDefaultNames} from 'infamous/html'
    import Motor from 'infamous/core/Motor'

    useDefaultNames()

    export default {
        data: () => ({
            rotation: 0
        }),
        mounted () {
            Motor.addRenderTask(() => {
                this.rotation++
            })
        }
    }
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
    .hello {
        width: 100%;
        height: 100%;
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
