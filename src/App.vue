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
          <v-text-field 
            v-model="query"
            name="input-2"
            label="Nodo a Buscar"
            value="Input text"
            class="input-group--focused"
          ></v-text-field>
          
        </v-flex>
        <v-flex xs4 ma-5>
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
          label="Search result: Targets"></v-text-field>
        </v-flex>
        <v-flex xs5 ma-5>
          <v-subheader>Sources</v-subheader>
          <v-text-field 
          v-model="asTarget" 
          id="testing" 
          name="input-1" 
          label="Search Results: Sources"></v-text-field>
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
import network1 from "@/assets/network_sitc2_4digit.json";
import network2 from "@/assets/network_hs92_4digit.json";

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
      
      this.asSource = network1.edges.filter(el => {
        return el.source == this.query;
      }).map(a => a.target);
      
      this.asTarget = network1.edges.filter(el => {
        return el.target == this.query;
      }).map(a => a.source);;
      
      this.asSource2 = network2.edges.filter(el => {
        return el.source == this.query;
      }).map(a => a.target);
      
      this.asTarget2 = network2.edges.filter(el => {
        return el.target == this.query;
      }).map(a => a.source);
      

      this.asSource = this.asSource.concat (this.asSource2);
      this.asTarget = this.asTarget.concat (this.asTarget2);
    },
    fillElements (network, number){
      network.nodes.forEach(node => {
      this.elements.push({
        data: {
          id: node.id +"_"+ number
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
          source: edge.source+"_"+ number,
          target: edge.target+"_"+ number,
          strength: edge.strength
        }
      };
      this.elements.push(e);
    });
    }
  },
  data: () => ({
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
    this.fillElements (network1, "1");
    this.fillElements (network2, "2");
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