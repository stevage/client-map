<template lang="pug">
#map.absolute.absolute--fill
</template>

<script>
import mapboxgl from 'mapbox-gl';
import U from 'mapbox-gl-utils';
import { sheets2geojson } from 'sheets2geojson';
import { EventBus } from '../EventBus';
export default {
    async mounted() {
        // replace this Mapbox access token with your own
        mapboxgl.accessToken = 'pk.eyJ1Ijoic3RldmFnZSIsImEiOiJjazNmNGV5enAwMTF1M2tuejhtc2twcXo5In0.mLPrYIYJ2FiFZ3KMqVIj6w';
        const map = new mapboxgl.Map({
            container: 'map',
            // center: [144.96, -37.81],
            // zoom: 2,
            bounds: [-130,-40, 160, 40],
            style: 'mapbox://styles/mapbox/light-v9',
            renderWorldCopies: false
        });
        U.init(map, mapboxgl);
        window.map = map;
        window.app.Map = this;
        // map.fitBounds([-8,-40, 140, 40]);

        const sheetID = '2PACX-1vT24ChN5SayVkKVz4jWHg_l7nYoFuQbddcYjkYTVjMkN_VjVND-P0Mfz4rfh8y4twEhW9ZgqPgyye_1'
        const points = await sheets2geojson(sheetID);
        map.U.onLoad(() => {
            map.U.hide(map.getStyle().layers.filter(l => l['source-layer']==='marine_label').map(l => l.id));
            map.U.addGeoJSON('points', points);
            map.U.addCircle('points-circles', 'points', {
                circleColor: 'hsl(330,100%,40%)',
                // circleRadius: { stops: [[10,3], [12, 10]] }
                circleRadius:['match', ['get', 'Size'],
                    'Small', 4,
                    'Medium', 8,
                    'Big', 12,
                    4
                ]
            });
            map.U.hoverPointer('points-circles');
            map.on('click', 'points-circles', e => {
                console.log(e);
                window.app.FeatureInfo.feature = e.features[0];
            });
        });
    }
}
import 'mapbox-gl/dist/mapbox-gl.css';

</script>

<style scoped>
</style>