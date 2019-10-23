<template>
  <v-app>
    <v-app-bar app>
      <v-toolbar-title class="headline text-uppercase">
        <span>Vuetify</span>
        <span class="font-weight-light">MATERIAL DESIGN</span>
      </v-toolbar-title>
      <v-spacer></v-spacer>
      <v-btn text href="https://github.com/vuetifyjs/vuetify/releases/latest" target="_blank">
        <span class="mr-2">Latest Release</span>
      </v-btn>
    </v-app-bar>

    <v-content>
      <v-layout row>
        <v-flex xs4>
          <v-subheader>Busqueda:</v-subheader>
        </v-flex>
        <v-flex xs4>
          <v-text-field
            v-model="query"
            name="input-2"
            label="Label Text"
            value="Input text"
            class="input-group--focused"
          ></v-text-field>
          
        </v-flex>
        <v-flex xs4>
          <v-btn color="info" @click="search">Buscar</v-btn>
        </v-flex>
      </v-layout>
      <v-layout row>
        
        <v-flex xs5 ma-10>
          <v-subheader>Targets</v-subheader>
          <v-text-field 
          v-model="asSource" 
          id="testing" 
          name="input-1" 
          label="Label Text"></v-text-field>
        </v-flex>
        <v-flex xs5 ma-10>
          <v-subheader>Sources</v-subheader>
          <v-text-field 
          v-model="asTarget" 
          id="testing" 
          name="input-1" 
          label="Label Text"></v-text-field>
        </v-flex>
        </v-layout>
      <cytoscape ref="cy" :config="config" v-on:zoom="getZoom">
        <cy-element v-for="def in elements" :key="`${def.data.id}`" :definition="def" />
      </cytoscape>
    </v-content>
  </v-app>
</template>

<script>
//import HelloWorld from "./components/HelloWorld";
import network from "@/assets/network_sitc2_4digit.json";
export default {
  name: "App",
  components: {
    //HelloWorld
  },
  watch: {},
  methods: {
    getZoom() {
      // this.$refs.cy.cy.then(c => {
        
      // });
    },
    afterCreated(cy) {
      this.cy = cy;
    },
    search() {
      
      this.asSource = network.edges.filter(el => {
        return el.source === this.query;
      }).map(a => a.target);;
      this.asTarget = network.edges.filter(el => {
        return el.target === this.query;
      }).map(a => a.source);;
      
    }
  },
  data: () => ({
    query: "",
    asSource: null,
    asTarget: null,
    cy: null,
    config: {
      style: [
        {
          selector: "node",
          style: {
            "background-color": "#666",
            label: "data(id)"
          }
        },
        {
          selector: "edge",
          style: {
            width: 3,
            "line-color": "#ccc",
            "target-arrow-color": "#ccc",
            "target-arrow-shape": "triangle"
          }
        }
      ],
      layout: {
        name: "grid",
        rows: 1,
        height: 1800
      },
      zoom: 0.2,
      pan: { x: 100, y: -300 }
    },
    elements: []
  }),

  mounted() {
    //this.cy.fit ();
    this.$refs.cy.cy.then(c => {
      c.container().style.height = "1000px";
      c.fit();
    });
  },
  created() {
    network.nodes.forEach(node => {
      this.elements.push({
        data: {
          id: node.id
        },
        position: {
          x: node.x,
          y: node.y
        }
      });
    });
    let i = 0;
    network.edges.forEach(edge => {
      this.elements.push({
        data: {
          id: "e_" + ++i,
          source: edge.source,
          target: edge.target,
          width: edge.strength
        }
      });
    });
  }
};
</script>
<style scoped>
#cy {
  width: 300px;
  height: 800px;
  display: block;
}
</style>