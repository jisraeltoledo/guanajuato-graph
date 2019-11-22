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
        <v-flex xs4 ma-5>
          <v-select :items="items" v-model="net"></v-select>
        </v-flex>
        <v-flex xs4 ma-5>
          <v-text-field
            v-model="query"
            name="input-2"
            label="Nodo a Buscar"
            value="Input text"
            class="input-group--focused"
          ></v-text-field>
        </v-flex>
        <v-flex xs2 ma-5>
          <v-btn color="info" @click="search">Buscar</v-btn>
        </v-flex>
      </v-layout>
      <v-layout row>
        <v-flex xs5 ma-5>
          <v-subheader>Targets</v-subheader>
          <v-text-field
            v-model="asSource"
            id="testing"
            name="input-1"
            label="Search result: Targets"
          ></v-text-field>
        </v-flex>
        <v-flex xs5 ma-5>
          <v-subheader>Sources</v-subheader>
          <v-text-field
            v-model="asTarget"
            id="testing"
            name="input-1"
            label="Search Results: Sources"
          ></v-text-field>
        </v-flex>
      </v-layout>
      <cytoscape ref="cy1" :config="config" v-on:zoom="getZoom" v-show="net=='sitc2'">
        <cy-element v-for="def in elements1" :key="`${def.data.id}`" :definition="def" />
      </cytoscape>
      <cytoscape ref="cy2" :config="config" v-on:zoom="getZoom" v-show="net=='hs92'">
        <cy-element v-for="def in elements2" :key="`${def.data.id}`" :definition="def" />
      </cytoscape>
    </v-content>
  </v-app>
</template>

<script>
//import HelloWorld from "./components/HelloWorld";
import network1 from "@/assets/network_sitc2_4digit.json";
import network2 from "@/assets/network_hs92_4digit.json";

export default {
  name: "App",
  components: {
    //HelloWorld
  },
  watch: {
  },
  data: () => ({
    net: "sitc2",
    items: ["hs92", "sitc2"],
    i: 0,
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
            width: "data(strength)",
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
    elements1: [],
    elements2: []
  }),
  methods: {
    getZoom() {
      // this.$refs.cy.cy.then(c => {
      // });
    },
    search() {
      if (this.net === "sitc2") {
        this.searchOn(network1);
      } else {
        this.searchOn(network2);
      }
    },
    searchOn(network) {
      this.asSource = network.edges
        .filter(el => {
          return el.source == this.query;
        })
        .map(a => a.target);

      this.asTarget = network.edges
        .filter(el => {
          return el.target == this.query;
        })
        .map(a => a.source);
    },
    fillElements(network, elements) {
      network.nodes.forEach(node => {
        elements.push({
          data: {
            id: node.id
          },
          position: {
            x: node.x,
            y: node.y
          }
        });
      });

      network.edges.forEach(edge => {
        let e = {
          data: {
            id: "e_" + ++this.i,
            source: edge.source,
            target: edge.target,
            strength: edge.strength
          }
        };
        elements.push(e);
      });
    },
    fit() {
      if (this.$refs.cy1) {
        this.$refs.cy1.cy.then(c => {
          c.container().style.height = "1000px";
          c.fit();
        });
      }

      if (this.$refs.cy2) {
        this.$refs.cy2.cy.then(c => {
          c.container().style.height = "1000px";
          c.fit();
        });
      }
    }
  },
  mounted() {
    this.fit();
  },
  created() {
    this.fillElements(network1, this.elements1);
    this.fillElements(network2, this.elements2);
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