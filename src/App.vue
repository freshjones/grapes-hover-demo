<template>
  <div id="app">
    <div id="editor"></div>
  </div>
</template>

<script>
import 'grapesjs/dist/css/grapes.min.css';
import grapesjs from 'grapesjs';

export default {
  name: 'App',
  mounted () {

    const customComponentType = editor => {
      editor.DomComponents.addType('custom-site-element', {
        isComponent: el => el.tagName === 'BLAH',
        model: {
          defaults: {
            editable: false,
            selectable: false,
            propagate: ['editable', 'selectable'],
            tagName: 'blah',
          }
        }
      });
    };


    let editor = grapesjs.init({
      container: '#editor',
      fromElement: true,
      width: 'auto',
      storageManager: false,
      plugins: [ customComponentType ],
    });


    editor.BlockManager.add("custom-component",
      {
        label: 'Custom Component',
        tab: "1",
        category: "Others",
        type: "default",
        attributes: {
          class: 'fa fa-ellipsis-h',
          title: 'Custom Component'
        },
        content: `
        <blah style="position:relative; display: block;">
          <h1 data-gjs-hoverable="false">Header test</h1>
          <p data-gjs-hoverable="false">Paragraph test</p>
        </blah>`
    });

    editor.on(`component:hovered`, component => {
      if (component.attributes.type != 'custom-site-element') {
          return;
      }
      component.append(`<div class="hover-state" style="position:absolute; top:0; left:0; width:100%; height:100%; background:rgba(0,0,0,0.5); z-index:100; display:flex; justify-content: center; align-items:center; backdrop-filter: blur(3px);" data-gjs-hoverable="false"><button data-gjs-hoverable="false" onClick="alert('test')">Edit SWE</button></div>`)
    })

    editor.on(`component:unhovered`, component => {
      if (component.attributes.type != 'custom-site-element') {
          return;
      }
      component.get('components').each(child => {
        let classes = child.getClasses();
        if(classes.includes('hover-state')) {
          child.remove()
        }
      });
    })

  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
