
<script setup>
import { ref, onMounted } from 'vue';


let acceleration = ref({});
let rotation = ref({});

let mouseX = ref(0);
let mouseY = ref(0);
let speed = ref(0);
let mouseDownEvent = ref({});

let location = ref({});

const startTime = Date.now();
let elapsedTime = ref(0);

let batteryLevel = ref(0.0);
let batteryCharging = ref(false);

let totalClicks = ref(0);
let lastKey = ref('');
let keysPressed = ref(new Set()); // A set to keep track of currently pressed keys

let display = ref('')

const formattedDisplay = computed(() => {
  return display.value.replace(/\n/g, '<br>');
});

let isMounted = ref(false);
let mobile = ref(false);

onMounted(() => {

   // Add key to the set when pressed
   document.addEventListener('keydown', (event) => {
    keysPressed.value.add(event.key);
    updateDisplay();
  });

   // Remove key from the set when released
   document.addEventListener('keyup', (event) => {
    keysPressed.value.delete(event.key);
    updateDisplay();
  });

  isMounted = true;

  document.addEventListener('mousemove', (event) => {
    speed.value = Math.sqrt(Math.pow(event.movementX, 2) + Math.pow(event.movementY, 2));
    mouseX.value = event.clientX;
    mouseY.value = event.clientY;
  });

  document.addEventListener('click', () => {
    totalClicks.value++;
  });

  document.addEventListener('keydown', (event) => {
    lastKey.value = event.key;
  });

  document.addEventListener('mousedown', (event) => {
  mouseDownEvent.value = event.button === 0 ? 'Left' : event.button === 1 ? 'Middle' : 'Right';
});

navigator.geolocation.getCurrentPosition((position) => {
  location.value = position;
});


  document.addEventListener('click', (event) => {
  if (typeof DeviceMotionEvent.requestPermission === 'function') {
    DeviceMotionEvent.requestPermission()
      .then(response => {
        if (response === 'granted') {
          console.log('Motion permission granted on parent page');
          // Optionally, notify the iframe that permission was granted
          const iframe = document.getElementById('yourIframeId');
          iframe.contentWindow.postMessage('motion-permission-granted', '*');
          mobile.value = true;
        } else {
          mobile.value = false;
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

acceleration.value = event.acceleration;
rotation.value = event.rotationRate;

}

});


setInterval(updateDisplay, 10);

});



function updateDisplay(){

  navigator.getBattery().then(battery => {
    if(battery){
  batteryLevel.value = battery.level;
  batteryCharging.value = battery.charging;
}
});

  elapsedTime.value = Math.floor((Date.now() - startTime) / 1000); // Time in seconds
  

display.value = ``;


  display.value += `User Agent: ${navigator.userAgent};
  Acceleration X: ${Math.round(acceleration.value.x)};
  Acceleration Y: ${Math.round(acceleration.value.y)};
  Acceleration Z: ${Math.round(acceleration.value.z)};
  Rotation Alpha: ${Math.round(rotation.value.alpha)};
  Rotation Beta: ${Math.round(rotation.value.beta)};
  Rotation Gamma: ${Math.round(rotation.value.gamma)};
  Device Orientation: ${window.orientation}°;
  Mouse X: ${Math.round(mouseX.value)};
  Mouse Y: ${Math.round(mouseY.value)};
  Mouse Button: ${mouseDownEvent.value};
  Mouse Speed: ${Math.round(speed.value)}px/s;
  Location: Lat ${location.value.coords.latitude}, Lon ${location.value.coords.longitude};
  Total Clicks: ${totalClicks.value};
  Keys Held: ${Array.from(keysPressed.value).join(' + ')};
  Last Key Pressed: ${lastKey.value};
  Time Since Page Load: ${elapsedTime.value} seconds;
  Battery Level: ${(batteryLevel.value * 100).toFixed(0)}%;
  Charging: ${batteryCharging.value ? 'Yes' : 'No'};
  Network Type: ${navigator.connection.effectiveType};
  Language: ${navigator.language};
  Screen Size: ${window.innerWidth}x${window.innerHeight};
`


display.value = display.value.replace(/\n/g, '<br>');
}




</script>


<template>
  <div>

<p v-html="formattedDisplay">


</p>
    


  </div>
</template>



<style>

p{
color: #83AC73;
font-size: 2.4vw;
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
