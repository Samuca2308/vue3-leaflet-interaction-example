<template>
    <div>
        <article id="map">
        </article>
        <span>Press z to undo; Press r to restart</span>
        <span>Área demarcada: {{polygonArr}}</span>
    </div>
</template>

<script>
import "leaflet/dist/leaflet.css";
import L from "leaflet";


export default {
    name: 'MapComponent',
    data() {
        return {
            map: null,
            polygonCheck: null,
            polygonArr: []
        }
    },
    props: {
        // Recebe a Localização inicial do mapa como um prop
        location: {
            type: Array,
            default: () => [51.505, -0.09]
        }
    },
    methods: {
        initMap() {
            // Inicializa o nosso elemento de mapa, associando ele tanto a variável dentro do arquivo quanto ao
            // elemento gráfico de id "map", também configura a parte gráfica interna do elemento de mapa com
            // o layer gratuito da openstreetmap
            this.map = L.map("map").setView(this.location, 10);
            L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png', {
                maxZoom: 19,
                attribution: '&copy; <a href="https://openstreetmap.org/copyright">OpenStreetMap contributors</a>'
                }
            ).addTo(this.map);

            // Associa funções do arquivo a determinadas açoes dentro do mapa
            this.map.on('click', this.onMapClick)
            this.map.on('keypress', this.onKeypress)
        },

        refreshPolygon() {
            // Remove a instância anterior (caso existente) e cria uma nova geometria para um polígono dentro do
            // mapa referente a área selecionada pelo usuário
            this.polygonCheck != null ? this.map.removeLayer(this.polygonCheck) : '',
            this.polygonCheck = L.polygon(this.polygonArr).addTo(this.map),
            this.map.addLayer(this.polygonCheck)
        },
        onMapClick(e) {
            // Cria um novo ponto nas coordenadas do clique e o integra ao polígono
            this.polygonArr.push([e.latlng.lat, e.latlng.lng]),
            this.refreshPolygon()
        },
        onKeypress(e) {
            // Isola a tecla pressionada e transforma a geometria de nosso polígono
            e.originalEvent.key == 'z' ? this.polygonArr.pop() :
            e.originalEvent.key == 'r' ? this.polygonArr = [] : '',
            this.refreshPolygon()
        }
    },
    mounted() {
        this.initMap();
    },
}
</script>

<style scoped>
    div {
        display: flex;
        flex-direction: column;
        align-items: center;
    }

    #map {
        height: 350px;
        aspect-ratio: 4 / 3;
        border-radius: 12px;
    }
</style>