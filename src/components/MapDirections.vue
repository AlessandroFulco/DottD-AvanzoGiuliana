<template>
  <div id="map" class="map"></div>
</template>

<script>
import L from "leaflet";
import "leaflet/dist/leaflet.css";
import "leaflet-routing-machine";
import "leaflet-routing-machine/dist/leaflet-routing-machine.css";

export default {
  name: "LeafletMap",
  props: {
    // Accept either [[lat,lng], ...] or [{lat,lng}, ...]
    waypoints: {
      type: Array,
      default: () => [],
    },
  },
  data() {
    return {
      map: null,
      routing: null,
    };
  },
  mounted() {
    this.map = L.map("map").setView([40.92586952296414, 14.301086851778948], 20);

    L.tileLayer("https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png", {
      attribution: "&copy; OpenStreetMap contributors",
      maxZoom: 19,
    }).addTo(this.map);

    this.routing = L.Routing.control({
      waypoints: this.normalizeWaypoints(this.waypoints),
      lineOptions: {
        styles: [{ color: "blue", weight: 5, opacity: 0.8 }],
      },
      show: false,
      addWaypoints: false,
      draggableWaypoints: false,
      fitSelectedRoutes: true,
      routeWhileDragging: false,
    }).addTo(this.map);

    // Optional: keep the map fitted on every new route
    this.routing.on("routesfound", (e) => {
      if (e.routes?.[0]?.bounds) this.map.fitBounds(e.routes[0].bounds);
    });
  },
  watch: {
    waypoints: {
      deep: true,
      handler(newVal) {
        if (!this.routing) return;
        const latLngs = this.normalizeWaypoints(newVal);
        console.log(latLngs);
        // If you want to allow clearing to <2 points:
        if (!latLngs || latLngs.length === 0) {
          this.routing.setWaypoints([]);
          return;
        }

        this.routing.setWaypoints(latLngs);
      },
    },
  },
  beforeUnmount() {
    if (this.map) this.map.remove();
  },
  methods: {
    normalizeWaypoints(points) {
      if (!Array.isArray(points)) return [];
      return points
          .map((p) =>
              Array.isArray(p)
                  ? L.latLng(p[0], p[1])
                  : (p && ("lat" in p) && ("lng" in p)) ? L.latLng(p.lat, p.lng) : null
          )
          .filter(Boolean);
    },
  },
};
</script>

<style scoped>
.map {
  width: 100%;
  height: 500px;
  border-radius: 10px;
}
</style>
