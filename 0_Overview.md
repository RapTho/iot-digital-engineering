# Overview

## Course content

This course will enable you to:

- Build your own mosquitto container image
- Deploy the mosquitto broker on IBM Code Engine
- Develop a publisher app in Python
- Develop and deploy your subscriber app in Python
- Persist messages via the subscriber app in an IBM Cloudant database

## Sequence Diagram

<!-- <style>
  .light-theme {
    display: none;
  }
  @media (prefers-color-scheme: dark) {
    .light-theme {
      display: block;
    }
    .dark-theme {
      display: none;
    }
  }
</style>

<picture class="light-theme">
  <img src="./files/sequence-diagram_light.svg" alt="Diagram for Light Theme">
</picture>

<picture class="dark-theme">
  <img src="./files/sequence-diagram_dark.svg" alt="Diagram for Dark Theme">
</picture> -->

<picture>
  <img id="theme-image" src="./files/sequence-diagram_light.svg" alt="Diagram">
</picture>
<script>
  const prefersDarkScheme = window.matchMedia("(prefers-color-scheme: dark)").matches;
  const imageElement = document.getElementById("theme-image");

    if (prefersDarkScheme) {
    imageElement.src = "./files/sequence-diagram_dark.svg";
    } else {
    imageElement.src = "./files/sequence-diagram_light.svg";
    }

</script>

<!-- <picture>
    <source srcset="./files/sequence-diagram_light.svg" media="(prefers-color-scheme: dark)">
    <source srcset="./files/sequence-diagram_dark.svg" media="(prefers-color-scheme: light)">
    <img src="./files/sequence-diagram_light.svg" alt="Diagram">
</picture> -->
