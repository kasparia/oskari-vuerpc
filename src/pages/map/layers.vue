<template>
  <div>
    <h2>{{ title }}</h2>
    <!-- RPC functions / getAllLayers() --> 
    <h3>Get current map layers</h3>
    <p>RPC API client has a function for listing available map layers: getMapLayers().</p>
    <DocumentationLink type="bundle" :apiDoc="apiDocPageRPC">Documentation for RPC functions</DocumentationLink>

    <CodeSnippet>
channel.getAllLayers(function (data) {
  channel.log('GetAllLayers:', data);
});
    </CodeSnippet>
    <RunExampleButton @click="getAllLayers">Show layers in log</RunExampleButton>
    
    <!-- ChangeMapLayerOpacityRequest --> 
    <h3>Change layer opacity</h3>
    <p>The request {{ requestNameOpacity }} can be used to change layer opacity programmatically.</p>

    <CodeSnippet>
channel.getAllLayers(function (layers) {
  // get the bottom layer
  var layerId = layers[0].id;
  var currentOpacity = layers[0].opacity;
  var newOpacity = currentOpacity !== 100 ? 100 : 50;
  channel.postRequest('{{ requestNameOpacity }}', [layerId, newOpacity]);
});
    </CodeSnippet>

    <DocumentationLink type="request" :apiDoc="apiDocPageOpacity">Documentation for {{ requestNameOpacity }}</DocumentationLink>

    <RunExampleButton @click="toggleOpacity">Toggle opacity</RunExampleButton>
  </div>
</template>

<script>
const title = 'Map Layers';
const apiDocPageRPC = 'framework/rpc';

const requestNameOpacity = 'ChangeMapLayerOpacityRequest';
const apiDocPageOpacity = 'mapping/mapmodule/request/changemaplayeropacityrequest.md';

const apiDocPageVisibility = 'mapping/mapmodule/request/MapModulePlugin.MapLayerVisibilityRequest.md';

export default {
  name: 'MapLayers',
  label: title,
  data () {
    return {
      title,
      apiDocPageRPC,
      requestNameOpacity,
      apiDocPageOpacity,
      apiDocPageVisibility
    }
  },
  methods: {
    getAllLayers () {
      this.$root.channel.getAllLayers((data) => {
        this.$root.channel.log('GetAllLayers: ', data);
      });
    },
    toggleOpacity () {
      const me = this;
      this.$root.channel.getAllLayers(function (layers) {
        const layerId = layers[0].id;
        const currentOpacity = layers[0].opacity;
        var newOpacity = currentOpacity !== 100 ? 100 : 50;

        me.$root.channel.postRequest(requestNameOpacity, [layerId, newOpacity]);
        me.$root.channel.log(requestNameOpacity + ' sent with parameters: ', [layerId, newOpacity]);
      });
    }
  },
  beforeDestroy: () => {
    // Clean up when user leaves the example
    // NOTE! We don't have this.$root here so relying on global channel variable
    // const channel = this.$root.channel;
    channel.resetState(() => {
        channel.log('Map state reset on exiting the example: "' + title + '"');
    });
  }
}
</script>
