<template>
    <div id="wrapper">
        <canvas id="mapCanvas" />
    </div>
</template>

<script lang="ts">
import { MapView } from '@here/harp-mapview';
import { GeoCoordinates, sphereProjection } from '@here/harp-geoutils';
import { MapControls, MapControlsUI } from '@here/harp-map-controls';
import { OmvDataSource, APIFormat } from '@here/harp-omv-datasource';
import { Theme } from '@here/harp-datasource-protocol';

import { defineComponent, onMounted } from 'vue';
export default defineComponent({
    name: 'Harp',
    props: {},
    setup(props, context) {
        const theme: Theme = {
            sky: {
                type: 'gradient',
                topColor: '#161719',
                bottomColor: '#262829',
                groundColor: '#262829',
                monomialPower: 1,
            },
            styles: {
                sample: [
                    {
                        id: 'waterarea',
                        technique: 'fill',
                        layer: 'water',
                        color: '#aaccff',
                    },
                    {
                        id: 'road',
                        technique: 'solid-line',
                        layer: 'transportation',
                        color: '#ffffff',
                        lineWidth: [
                            'interpolate',
                            ['linear'],
                            ['zoom'],
                            10,
                            100,
                            14,
                            2,
                        ],
                        outlineColor: '#000000',
                        outlineWidth: [
                            'interpolate',
                            ['linear'],
                            ['zoom'],
                            10,
                            10,
                            14,
                            2.1,
                        ],
                    },
                    {
                        id: 'boundary',
                        technique: 'solid-line',
                        layer: 'boundary',
                        color: '#ff0000',
                        lineWidth: [
                            'interpolate',
                            ['linear'],
                            ['zoom'],
                            5,
                            500,
                            10,
                            50,
                            14,
                            2,
                        ],
                    },
                    {
                        id: 'extrudedBuildings',
                        description: 'extruded buildings',
                        technique: 'extruded-polygon',
                        layer: 'building',
                        minZoomLevel: 14,
                        renderOrder: 2000,
                        height: ['get', 'render_height'],
                        color: '#ffffff',
                        roughness: 1,
                        metalness: 0.8,
                        emissive: '#78858C',
                        emissiveIntensity: 0.85,
                        footprint: true,
                        maxSlope: 0.8799999999999999,
                        lineWidth: 1,
                        lineColor: '#ffffff',
                        lineColorMix: 0.6,
                        fadeNear: 0.9,
                        fadeFar: 1,
                        lineFadeNear: -0.75,
                        lineFadeFar: 1,
                    },
                ],
            },
        };
        onMounted(() => {
            const canvas = document.getElementById(
                'mapCanvas',
            ) as HTMLCanvasElement;

            // instantiate MapView
            const map = new MapView({
                canvas: canvas,
                theme: theme,
                projection: sphereProjection,
                target: new GeoCoordinates(35.68, 139.77),
                zoomLevel: 15,
                minZoomLevel: 5,
                maxZoomLevel: 18,
            });

            // add controls
            const controls = new MapControls(map);
            const ui = new MapControlsUI(controls, { zoomLevel: 'input' });
            if (canvas.parentElement !== null) {
                canvas.parentElement.appendChild(ui.domElement);
            }

            // add vectortile datasource
            // https://tile.openstreetmap.jp/data/japan/{z}/{x}/{y}.pbf
            const dataSource = new OmvDataSource({
                baseUrl: 'https://tile.openstreetmap.jp/data/japan',
                apiFormat: APIFormat.TomtomV1,
                styleSetName: 'sample',
            });
            map.addDataSource(dataSource);
        });
    },
});
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
#mapCanvas {
    height: 100%;
    width: 100%;
}
</style>
