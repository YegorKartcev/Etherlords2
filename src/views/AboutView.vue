<template>
  <div class="about">
    <div class="ribbon"><span><a class="ribbon-link" href="https://github.com/YegorKartcev">GitHub</a></span></div>
    <div id="container">
      <a class="" href="https://vladivostok.hh.ru/resume/14e23ea7ff084341460039ed1f594951457547">
        <img class="blob" alt="Vue logo" src="../assets/Yegor.jpeg">
      </a>
      <div class="mt-4">
        <svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" fill="currentColor" class="bi bi-arrow-up-circle" viewBox="0 0 16 16">
          <path fill-rule="evenodd" d="M1 8a7 7 0 1 0 14 0A7 7 0 0 0 1 8zm15 0A8 8 0 1 1 0 8a8 8 0 0 1 16 0zm-7.5 3.5a.5.5 0 0 1-1 0V5.707L5.354 7.854a.5.5 0 1 1-.708-.708l3-3a.5.5 0 0 1 .708 0l3 3a.5.5 0 0 1-.708.708L8.5 5.707V11.5z"/>
        </svg>
        click
        <svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" fill="currentColor" class="bi bi-arrow-up-circle" viewBox="0 0 16 16">
          <path fill-rule="evenodd" d="M1 8a7 7 0 1 0 14 0A7 7 0 0 0 1 8zm15 0A8 8 0 1 1 0 8a8 8 0 0 1 16 0zm-7.5 3.5a.5.5 0 0 1-1 0V5.707L5.354 7.854a.5.5 0 1 1-.708-.708l3-3a.5.5 0 0 1 .708 0l3 3a.5.5 0 0 1-.708.708L8.5 5.707V11.5z"/>
        </svg>
      </div>
    </div>
  </div>
</template>

<style>

.ribbon {
  position: absolute;
  right: 0px; top: 0px;
  z-index: 1;
  overflow: hidden;
  width: 150px; height: 150px;
  text-align: right;
}
.ribbon span {
  font-size: 16px;
  font-weight: bold;
  color: #FFF;
  /* text-transform: uppercase; */
  text-align: center;
  line-height: 40px;
  transform: rotate(45deg);
  -webkit-transform: rotate(45deg);
  width: 250px;
  display: block;
  background: #000000;
  background: linear-gradient(#000000 0%, #2e2e2e 100%);
  box-shadow: 0 3px 10px -5px rgba(0, 0, 0, 1);
  position: absolute;
  top: 25px; right: -80px;
}
.ribbon-link {
  color: #fff;
}

.blob {
	box-shadow: 0 0 0 0 rgb(101, 192, 253);
	animation: pulse 2s infinite;
  border-radius: 50%;
  height: 300px;
  width: 310px;
}

@keyframes pulse {
	0% {
		transform: scale(0.95);
		box-shadow: 0 0 0 0 rgba(101, 192, 253, 0.7);
	}

	70% {
		transform: scale(1);
		box-shadow: 0 0 0 20px rgba(101, 192, 253, 0);
	}

	100% {
		transform: scale(0.95);
		box-shadow: 0 0 0 0 rgba(101, 192, 253, 0);
	}
}
</style>