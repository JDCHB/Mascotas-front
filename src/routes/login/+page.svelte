<script>
  import Footer from "../../lib/components/footer.svelte";
  import Navbar from "../../lib/components/Navbar.svelte";
  import { onMount } from "svelte";

  let v_usuario = "";
  let v_password = "";
  let v_nombre = "";
  let v_apellido = "";
  let v_documento = "";
  let v_telefono = "";
  let v_email = "";
  let v_rol = 2;
  let v_estado = true;
  let error = null;

  // Referencias a los contenedores de los loader
  let loginLoader;
  let registerLoader;

  // Función para mostrar el loader
  // Función para mostrar el loader
  function showLoader(loader) {
    if (loader) {
      loader.style.display = "flex";
    }
  }

  // Función para ocultar el loader
  function hideLoader(loader) {
    if (loader) {
      loader.style.display = "none";
    }
  }

  async function Login() {
    const v_usuario = document.getElementById("correo").value;
    const v_password = document.getElementById("contraseña").value;

    try {
      showLoader(loginLoader); // Mostrar loader
      const response = await fetch(
        "https://proyectomascotas.onrender.com/login_generate_token",
        {
          method: "POST",
          headers: {
            "Content-Type": "application/json",
          },
          body: JSON.stringify({
            email: v_usuario,
            password: v_password,
          }),
        },
      );

      const data = await response.json();
      hideLoader(loginLoader); // Ocultar loader

      if (response.ok) {
        const { access_token, user_data } = data; // Extraer token y datos del usuario

        // Guardar token y datos en localStorage
        localStorage.setItem("access_token", access_token);
        localStorage.setItem("user_data", JSON.stringify(user_data));

        if (user_data.id_rol == 1) {
          Swal.fire({
            title: "Inicio de Sesión Exitoso",
            text: "¡Bienvenido al Sistema de Administración!",
            icon: "success",
            confirmButtonText: "OK",
          }).then(() => {
            window.location.href = "/administrador";
          });
        } else if (user_data.id_rol == 2) {
          Swal.fire({
            title: "Inicio de Sesión Exitoso",
            text: "¡Bienvenido al Sistema!",
            icon: "success",
            confirmButtonText: "OK",
            customClass: {
              popup: "swal-popup", // Clase para personalizar el popup de la alerta
              title: "custom-title", // Clase personalizada para el título
            },
          }).then(() => {
            window.location.href = "/usuario";
          });
        }
      } else {
        Swal.fire({
          icon: "error",
          title: "Oops...",
          text: data.detail || "Usuario o Contraseña Incorrecto!",
          customClass: {
            popup: "swal-popup", // Clase para personalizar el popup de la alerta
            title: "custom-title", // Clase personalizada para el título
          },
        });
      }
    } catch (e) {
      console.error("Error en la solicitud:", e.message);
      hideLoader(loginLoader);
      Swal.fire({
        icon: "error",
        title: "Error",
        text: "Hubo un problema con el inicio de sesión. Intenta nuevamente.",
      });
    }
  }

  async function Register() {
    try {
      // Muestra el cuadro de confirmación antes de proceder con el registro
      const result = await Swal.fire({
        title: "¿Estás seguro?",
        text: "¡Desea registrarse a PETTRACKER!?",
        icon: "warning",
        showCancelButton: true,
        confirmButtonColor: "#3085d6",
        cancelButtonColor: "#d33",
        confirmButtonText: "Sí, registrarse!",
        customClass: {
          popup: "swal-popup", // Clase para personalizar el popup de la alerta
          title: "custom-title", // Clase personalizada para el título
        },
      });
      // Si el usuario confirma, continuamos con el registro
      if (result.isConfirmed) {
        showLoader(registerLoader); // Mostrar loader al comenzar el registro
        const response = await fetch(
          "https://proyectomascotas.onrender.com/create_user",
          {
            method: "POST",
            headers: {
              "Content-Type": "application/json",
            },
            body: JSON.stringify({
              email: v_email,
              password: v_password,
              nombre: v_nombre,
              apellido: v_apellido,
              documento: v_documento,
              telefono: v_telefono,
              id_rol: v_rol,
              estado: v_estado,
            }),
          },
        );

        const data = await response.json();
        console.log(data);

        hideLoader(registerLoader); // Ocultar loader al terminar el registro

        if (response.ok) {
          Swal.fire({
            title: "¡Registrado!,¡Bienvenido " + v_nombre + "!",
            icon: "success",
            customClass: {
              popup: "swal-popup", // Clase para personalizar el popup de la alerta
              title: "custom-title", // Clase personalizada para el título
            },
          });
        } else {
          alert("Error en el registro");
        }
      } else {
        console.log("Registro Cancelado");
      }
    } catch (e) {
      error = e.message;
      hideLoader(registerLoader); // Ocultar loader si ocurre un error
      alert("Error en la solicitud: " + error);
    }
  }
</script>

<Navbar></Navbar>
<section>
  <div class="wrapper d-flex align-items-center justify-content-center vh-100">
    <div class="card-switch">
      <label class="switch">
        <input type="checkbox" class="toggle" />
        <span class="slider"></span>
        <span class="card-side"></span>
        <div class="flip-card__inner">
          <div class="flip-card__front">
            <div class="title">INICIO DE SESION</div>
            <form on:submit|preventDefault={Login} class="flip-card__form">
              <input
                id="correo"
                class="flip-card__input"
                bind:value={v_usuario}
                placeholder="Email"
                type="email"
                required
              />
              <input
                id="contraseña"
                class="flip-card__input"
                bind:value={v_password}
                placeholder="Password"
                type="password"
                required
              />
              <button class="flip-card__btn">Ingresar</button>
            </form>
            <!-- Loader del login -->
            <div class="loader-container" bind:this={loginLoader}>
              <div class="loader-dog-head">
                <div class="ear left-ear"></div>
                <div class="ear right-ear"></div>
                <div class="eye left-eye">
                  <div class="pupil"></div>
                </div>
                <div class="eye right-eye">
                  <div class="pupil"></div>
                </div>
                <div class="nose"></div>
                <div class="mouth"></div>
                <div class="tongue"></div>
              </div>
            </div>
          </div>
          <div class="flip-card__back small-card">
            <div class="title small-title">REGISTRO</div>
            <form on:submit|preventDefault={Register} class="flip-card__form">
              <input
                class="flip-card__input small-input"
                bind:value={v_nombre}
                placeholder="Name"
                type="text"
                required
              />
              <input
                class="flip-card__input small-input"
                bind:value={v_apellido}
                placeholder="Apellido"
                type="text"
                required
              />
              <input
                class="flip-card__input small-input"
                bind:value={v_documento}
                placeholder="Documento"
                type="text"
                required
              />
              <input
                class="flip-card__input small-input"
                bind:value={v_telefono}
                placeholder="Teléfono"
                type="tel"
                required
              />
              <input
                class="flip-card__input small-input"
                bind:value={v_email}
                placeholder="Email"
                type="email"
                required
              />
              <input
                class="flip-card__input small-input"
                bind:value={v_password}
                placeholder="Password"
                type="password"
                required
              />
              <button class="flip-card__btn small-btn">Confirmar</button>
            </form>
            <!-- Loader del registro -->
            <div class="loader-container" bind:this={registerLoader}>
              <div class="loader-dog-head">
                <div class="ear left-ear"></div>
                <div class="ear right-ear"></div>
                <div class="eye left-eye">
                  <div class="pupil"></div>
                </div>
                <div class="eye right-eye">
                  <div class="pupil"></div>
                </div>
                <div class="nose"></div>
                <div class="mouth"></div>
                <div class="tongue"></div>
              </div>
            </div>
          </div>
        </div>
      </label>
    </div>
  </div>

  <style>
    /* Estilos para Sweet Alert */
    /* Fondo blanco para la alerta SweetAlert */
    .swal-popup {
      background-color: white !important;
    }

    /* Fondo blanco para la alerta de éxito */
    .swal-popup-success {
      background-color: white !important;
    }
    /* Cambiar el color del título a negro */
    .custom-title {
      color: black !important; /* Asegúrate de que el color se aplique */
      text-align: center; /* Centrar el título */
    }

    /* Estilos para el loader de la cara de un perrito */
    .wrapper {
      --input-focus: #2d8cf0;
      --font-color: #323232;
      --font-color-sub: #666;
      --bg-color: #fff;
      --bg-color-alt: #666;
      --main-color: #323232;
      height: 100vh;
      display: flex;
      flex-direction: column;
      align-items: center;
      justify-content: center;
    }

    /* Contenedor para el loader */
    .loader-container {
      position: absolute;
      top: 0;
      left: 0;
      right: 0;
      bottom: 0;
      background: rgba(255, 255, 255, 0.8); /* Fondo semi-transparente */
      display: none; /* Oculto por defecto */
      justify-content: center;
      align-items: center;
      z-index: 100; /* Asegúrate de que esté encima de otros elementos */
    }

    .loader-dog-head {
      position: relative;
      width: 120px;
      height: 120px;
      background-color: #f4a261; /* Color de piel */
      border-radius: 50%; /* Forma de la cara */
      display: flex;
      justify-content: center;
      align-items: center;
      animation: bounce 2s infinite;
    }

    .ear {
      position: absolute;
      background-color: #f4a261;
      width: 40px;
      height: 60px;
      border-radius: 50%;
      top: 10px;
    }

    .left-ear {
      left: -20px;
      transform: rotate(-30deg);
    }

    .right-ear {
      right: -20px;
      transform: rotate(30deg);
    }

    .eye {
      position: absolute;
      background-color: #fff;
      width: 20px;
      height: 20px;
      border-radius: 50%;
      top: 35px;
    }

    .left-eye {
      left: 30px;
    }

    .right-eye {
      right: 30px;
    }

    .pupil {
      position: absolute;
      background-color: #000;
      width: 8px;
      height: 8px;
      border-radius: 50%;
      top: 6px;
      left: 6px;
      animation: blink 3s infinite;
    }

    .nose {
      position: absolute;
      background-color: #2d3436;
      width: 20px;
      height: 15px;
      border-radius: 50%;
      bottom: 45px;
      box-shadow: 0 2px 5px rgba(0, 0, 0, 0.3);
    }

    .mouth {
      position: absolute;
      width: 40px;
      height: 20px;
      border: 2px solid #2d3436;
      border-top: none;
      border-radius: 0 0 20px 20px;
      bottom: 30px;
    }

    .tongue {
      position: absolute;
      background-color: #e76f51;
      width: 16px;
      height: 24px;
      border-radius: 50%;
      bottom: 15px;
      animation: waggle 1.5s infinite ease-in-out;
    }

    /* Animaciones */
    @keyframes bounce {
      0%,
      100% {
        transform: translateY(0);
      }
      50% {
        transform: translateY(-10px);
      }
    }

    @keyframes blink {
      0%,
      80%,
      100% {
        transform: scaleY(1);
      }
      90% {
        transform: scaleY(0.1);
      }
    }

    @keyframes waggle {
      0%,
      100% {
        transform: rotate(0);
      }
      50% {
        transform: rotate(10deg);
      }
    }
    /* switch card */
    .switch {
      transform: translateY(-200px);
      position: relative;
      display: flex;
      flex-direction: column;
      justify-content: center;
      align-items: center;
      gap: 30px;
      width: 50px;
      height: 20px;
    }
    .card-side::before {
      position: absolute;
      content: "Ingresar";
      left: -70px;
      top: 0;
      width: 100px;
      text-decoration: underline;
      color: var(--font-color);
      font-weight: 600;
    }
    .card-side::after {
      position: absolute;
      content: "Registrarse";
      left: 70px;
      top: 0;
      width: 100px;
      text-decoration: none;
      color: var(--font-color);
      font-weight: 600;
    }
    .toggle {
      opacity: 0;
      width: 0;
      height: 0;
    }
    .slider {
      box-sizing: border-box;
      border-radius: 5px;
      border: 2px solid var(--main-color);
      box-shadow: 4px 4px var(--main-color);
      position: absolute;
      cursor: pointer;
      top: 0;
      left: 0;
      right: 0;
      bottom: 0;
      background-color: var(--bg-colorcolor);
      transition: 0.3s;
    }
    .slider:before {
      box-sizing: border-box;
      position: absolute;
      content: "";
      height: 20px;
      width: 20px;
      border: 2px solid var(--main-color);
      border-radius: 5px;
      left: -2px;
      bottom: 2px;
      background-color: var(--bg-color);
      box-shadow: 0 3px 0 var(--main-color);
      transition: 0.3s;
    }
    .toggle:checked + .slider {
      background-color: var(--input-focus);
    }
    .toggle:checked + .slider:before {
      transform: translateX(30px);
    }
    .toggle:checked ~ .card-side:before {
      text-decoration: none;
    }
    .toggle:checked ~ .card-side:after {
      text-decoration: underline;
    }
    /* card */
    .flip-card__inner {
      width: 300px;
      height: 350px;
      position: relative;
      background-color: transparent;
      perspective: 1000px;
      text-align: center;
      transition: transform 0.8s;
      transform-style: preserve-3d;
    }
    .toggle:checked ~ .flip-card__inner {
      transform: rotateY(180deg);
    }
    .toggle:checked ~ .flip-card__front {
      box-shadow: none;
    }
    .flip-card__front,
    .flip-card__back {
      padding: 20px;
      position: absolute;
      display: flex;
      flex-direction: column;
      justify-content: center;
      -webkit-backface-visibility: hidden;
      backface-visibility: hidden;
      background: lightgrey;
      gap: 20px;
      border-radius: 5px;
      border: 2px solid var(--main-color);
      box-shadow: 4px 4px var(--main-color);
    }
    .flip-card__back {
      width: 100%;
      transform: rotateY(180deg);
    }
    .flip-card__form {
      display: flex;
      flex-direction: column;
      align-items: center;
      gap: 20px;
    }
    .title {
      margin: 20px 0 20px 0;
      font-size: 25px;
      font-weight: 900;
      text-align: center;
      color: var(--main-color);
    }
    .flip-card__input {
      width: 250px;
      height: 40px;
      border-radius: 5px;
      border: 2px solid var(--main-color);
      background-color: var(--bg-color);
      box-shadow: 4px 4px var(--main-color);
      font-size: 15px;
      font-weight: 600;
      color: var(--font-color);
      padding: 5px 10px;
      outline: none;
    }
    .flip-card__input::placeholder {
      color: var(--font-color-sub);
      opacity: 0.8;
    }
    .flip-card__input:focus {
      border: 2px solid var(--input-focus);
    }
    .flip-card__btn:active,
    .button-confirm:active {
      box-shadow: 0px 0px var(--main-color);
      transform: translate(3px, 3px);
    }
    .flip-card__btn {
      margin: 20px 0 20px 0;
      width: 120px;
      height: 40px;
      border-radius: 5px;
      border: 2px solid var(--main-color);
      background-color: var(--bg-color);
      box-shadow: 4px 4px var(--main-color);
      font-size: 17px;
      font-weight: 600;
      color: var(--font-color);
      cursor: pointer;
    }

    /* Ajustes de tamaño específicos para flip-card__back */
    .small-card {
      width: 280px;
      height: 300px;
    }
    .small-title {
      font-size: 22px;
    }
    .small-input {
      width: 220px;
      height: 35px;
      padding: 4px 8px;
    }
    .small-btn {
      width: 100px;
      height: 35px;
      font-size: 15px;
    }
  </style>
</section>

<Footer></Footer>
