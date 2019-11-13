<template>
  <div ref='viewer'></div>
</template>

<script>
/* eslint-disable */
export default {
  name: 'WebViewer',
  props: {
    path: String,
    url: String,
    viewer_object: Object,
    colorArr: Array,
    layerArr: Array
  },
  mounted: function () {
    let _color = this.colorArr;
    let _layer = this.layerArr;
    let colorArr = {}; //this.$router.options.routes.find((data) => data.name == "color");
    let layerArr = {}; //this.$router.options.routes.find((data) => data.name == "layer");
    let vueObject = this;

    WebViewer({
        path: this.path,
        fullAPI: true,
        backendType: 'ems',
        initialDoc: this.url, // replace with your own PDF fille
      }, this.$refs.viewer).then((instance) => {
        // call apis here
        colorArr.viewerInstance = instance;
        layerArr.viewerInstance = instance;
        const docViewer = instance.docViewer;
        docViewer.on('documentLoaded', () => {
          const doc = docViewer.getDocument();
    
          doc.enableColorSeparations(true);
          doc.on('colorSeparationAdded', (event, colorData) => {
            if(!_color)
              _color = [];
            colorData.isSpot = colorData.color && colorData.color.filter((data) => data != 0).length == 1;
            colorData.enable_default = true;
            colorData._color = 'rgb(' + colorData.rgb.toString() + ')';
            _color.push(colorData);

            if(colorArr)
              colorArr.params = {colorData : _color};
          }); 

          doc.getLayersArray().then((layersArray) => {
            if(!_layer)
              _layer = [];
            _layer.every((data) => data.enable_default = true);
            _layer = layersArray;
            if(layerArr)
              layerArr.params = {layerData : _layer}
          });
        });

        instance.setTheme('dark');
      });
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
div {
  width: 100%; 
  height: 100vh;
}
</style>
