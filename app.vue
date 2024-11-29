
<script setup>
import {ref, onMounted} from 'vue';


let acceleration = ref({});

let rotation = ref({});

let display = ref('')

onMounted(() => {

  document.addEventListener('click', () => {
  if (typeof DeviceMotionEvent.requestPermission === 'function') {
    DeviceMotionEvent.requestPermission()
      .then(response => {
        if (response === 'granted') {
          console.log('Motion permission granted on parent page');
          // Optionally, notify the iframe that permission was granted
          const iframe = document.getElementById('yourIframeId');
          iframe.contentWindow.postMessage('motion-permission-granted', '*');
        } else {
          console.error('Motion permission denied on parent page');
        }
      })
      .catch(console.error);
  } else {
    console.log('DeviceMotionEvent.requestPermission is not available on this browser');
  }
}, { once: true }); // Run this only once after the first touch

  
addEventListener("devicemotion", (event) => {

if(event){

  console.log(event);

acceleration.value = event.acceleration;
rotation.value = event.rotationRate;

}

});


setInterval(() => {display.value = `
  
  Acceleration X: ${Math.round(acceleration.value.x)}
  Acceleration Y: ${Math.round(acceleration.value.y)}
  Acceleration Z: ${Math.round(acceleration.value.z)}
  Rotation Alpha: ${Math.round(rotation.value.alpha)}
  Rotation Beta: ${Math.round(rotation.value.beta)}
  Rotation Gamma: ${Math.round(rotation.value.gamma)}

`
}, 100
);


});




</script>


<template>
  <div>

<p>

{{ display }}

</p>
    


  </div>
</template>



<style>

p{
color: #83AC73;
font-size: 3vw;
font-weight: 600;
font-family: 'Courier New', Courier, monospace;
margin: 0;
}

body{
background-color: #2D302C;
margin: 0;
}

html{
  background: white;
}

body, html{
width: 100vw;
height: 100vw;
}

</style>
