<template>
	<main>
		<section class="cover-hero">
			<img src="/public/hero.png" alt="cover-hero">
			<h1>El secreto <br> de tu cocina</h1>
		</section>
		<section class="about">
			<div class="container flex">
				<nav>
					<a href="#" :class="{ active: activeFilter === 'todos' }" @click="changeFilter('todos')">TODOS</a>
					<a href="#" :class="{ active: activeFilter === 'productos' }" @click="changeFilter('productos')">PRODUCTOS</a>
					<a href="#" :class="{ active: activeFilter === 'recetas' }" @click="changeFilter('recetas')">RECETAS</a>
					<a href="#" :class="{ active: activeFilter === 'consejos' }" @click="changeFilter('consejos')">CONSEJOS</a>
				</nav>
				<div class="item flex">

					<article v-for="item in data" :key="item.id">
						<figure>
							<img :src="item.image" :alt="item.title">
						</figure>
						<h2>{{ item.title }}</h2>
						<p>{{ item.content }}</p>
					</article>

				</div>
			</div>
		</section>
		<section class="contactanos">
			<h2>Contactanós</h2>
			<form @submit.prevent="submitForm" class="flex">
				<div class="row flex">
					<div class="col">
						<label for="nombre">Nombre</label>
						<input v-model="form.firstname" type="text" id="nombre" name="nombre" :class="{ error: errors.firstname,  success: success.firstname }">
					</div>
					<div class="col">
						<label for="apellido">Apellido</label>
						<input v-model="form.lastname" type="text" id="apellido" name="apellido" :class="{ error: errors.lastname, success: success.lastname }">
					</div>
				</div>
				<div class="row flex">
					<div class="col">
						<label for="mail">Mail</label>
						<input v-model="form.email" type="email" id="mail" name="mail" :class="{ error: errors.email, success: success.email }">
					</div>
					<div class="col">
						<label for="telefono">Teléfono</label>
						<input v-model="form.phone" type="text" id="telefono" name="telefono" :class="{ error: errors.phone, success: success.phone }">
					</div>
				</div>
				<button type="submit">Enviar</button>
			</form>
		</section>
	</main>
</template>

<script setup>
import { ref, onMounted } from 'vue'
import axios from 'axios'

const data = ref(null)
const activeFilter = ref('todos')  

const form = ref({
	firstname: '',
	lastname: '',
	email: '',
	phone: ''
})

const errors = ref({
	firstname: false,
	lastname: false,
	email: false,
	phone: false
})

const success = ref({
	firstname: false,
	lastname: false,
	email: false,
	phone: false
})

const isEmpty = (value) => {
	return !value || value.trim() === ''
}

const validateForm = () => {
	let valid = true

	// Validar nombre
	if (isEmpty(form.value.firstname)) {
		errors.value.firstname = true
		success.value.firstname = false
		valid = false
	} else {
		errors.value.firstname = false
		success.value.firstname = true
	}

	// Validar apellido
	if (isEmpty(form.value.lastname)) {
		errors.value.lastname = true
		success.value.lastname = false
		valid = false
	} else {
		errors.value.lastname = false
		success.value.lastname = true
	}

	// Validar email
	const emailPattern = /^[^\s@]+@[^\s@]+\.[^\s@]+$/
	if (isEmpty(form.value.email) || !emailPattern.test(form.value.email)) {
		errors.value.email = true
		success.value.email = false
		valid = false
	} else {
		errors.value.email = false
		success.value.email = true
	}

	// Validar teléfono
	if (isEmpty(form.value.phone)) {
		errors.value.phone = true
		success.value.phone = false
		valid = false
	} else {
		errors.value.phone = false
		success.value.phone = true
	}

	return valid
}


const fetchData = async (params) => {
	const response = await axios.get(`https://5eed24da4cbc340016330f0d.mockapi.io/api/articles?filter=${params}`)
	data.value = response.data
}

const changeFilter = (filter) => {
	event.preventDefault() 
	activeFilter.value = filter
	const filterParam = filter === 'todos' ? '' : filter  
	fetchData(filterParam)
}

const submitForm = async () => {
	if (!validateForm()) {
		console.log('Formulario no válido')
		return
	}
	try {
		const response = await axios.post('https://5eed24da4cbc340016330f0d.mockapi.io/api/newsletter', form.value)
		console.log('Formulario enviado con éxito:', response.data)
	} catch (error) {
		console.error('Error al enviar el formulario:', error)
	}
}

onMounted(() => {
	fetchData('') 
})
</script>


<style scss>

	section.about{
		width: 100%;
		.container{
			align-items: flex-start;
		margin-top: -59px;
		z-index: 1;
		position: relative;
		}
	}

	section.cover-hero{
		position: relative;
		img{
			width: 100%;
			height: 100vh;
			object-fit: cover;
		}
		h1{
			position: absolute;
			top: 39%;
			left: 28%;
			transform: translate(-50%, -50%);

			font-family: "Caveat", cursive;
			font-size: 120px;
			font-style: normal;
			font-weight: 700;
			line-height: 100px;
			color: #3F454A;
		}
	}

	.item.flex{
		width: calc(100% - 274px);
		justify-content: flex-start;
		flex-wrap: wrap;
	}
	article{
		width: 270px;
		border-radius: 10px 10px 0px 0px;
		margin: 0 30px 30px 0;
		overflow: hidden;
		box-shadow: 0px 4px 40px rgba(0, 0, 0, 0.07);
		transition: all 0.3s ease;
		figure{
			margin-bottom: 39px;
			img{
				width: 100%;
				height: 100%;
			}
		}
		h2{
			padding: 0 26px 30px;
			color: var(--24272-a, #3F454A);
			font-family: "Open Sans", system-ui;
			font-size: 18px;
			font-style: normal;
			font-weight: 700;
			line-height: 24px; 
			letter-spacing: 0.18px;
		}
		p{
			padding: 0 26px 30px;
			color: #3F454A;
			font-family: "Open Sans", system-ui;
			font-size: 14px;
			font-style: normal;
			font-weight: 400;
			line-height: 24px;
		}
		
	}
	nav{
		width: 274px;
		display: flex;
		flex-direction: column;
		border-radius: 10px;
		background: #FFF;
		box-shadow: 0px 4px 40px 0px rgba(0, 0, 0, 0.07);
		margin-right: 30px;
		padding: 24px 23px;
		a{
			display: inline-block;
			color: #3F454A;
			font-family: "Open Sans", system-ui;
			font-size: 12px;
			font-style: normal;
			font-weight: 700;
			line-height: 23px; /* 191.667% */
			letter-spacing: 0.36px;
			text-transform: uppercase;
			&.active{
				color: #D8AD3D;
			}
		}
	}

	section.contactanos{
		width: 100%;
		h2{
			color: var(--24272-a, #3F454A);
			text-align: center;
			font-family: "Caveat", cursive;
			font-size: 80px;
			font-style: normal;
			font-weight: 700;
			line-height: 38px;
			margin-bottom: 57px;
		}
		form{
			margin: auto;
			width: 47%;
			flex-wrap: wrap;
			   .row{
					width: 	100%;
					.col{
						width: 50%;
						margin-right: 19.6px;
						margin-bottom: 9.86px;
						label{
							color: #24272A;
							font-family: "Open Sans", system-ui;
							font-size: 13px;
							font-style: normal;
							font-weight: 700;
							line-height: 24px;
							text-transform: uppercase;
							display: block;
							margin-bottom: 15px;
							
					}
					input{
						border: 1px solid var(--cfcfcf, #CFCFCF);
						background: #FFF;
						height: 50px;
						width: 100%;
					}
				}
			}
		}
	}

	section.contactanos{
		margin-top: 91px;
	}

	input.error {
		border: 2px solid red !important;
	}
	input.success {
	border: 1px solid #D8AD3D !important;
}
main{
	padding-bottom: 130px;
}

@media only screen and (min-width: 1024px) and (max-width: 1280px) {
  html {
    font-size: 14px;
  }
}

@media only screen and (min-width: 960px) and (max-width: 1024px) {
  html {
    font-size: 13px;
  }

  .container {
    max-width: 1024px !important;}
}
@media only screen and (min-width: 768px) and (max-width: 959px) {
  html {
    font-size: 11px;}

  body {
    height: 100vh;}

  .container {
    max-width: 959px!important;}
}

@media only screen and (max-width: 767px) {
	section.cover-hero {
		
		h1 {
			padding: 0 20px;
			top: 50%;
			left: 50%;
		}
	}
	nav{
		margin-bottom: 50px;
	}
	section.about {
    .container {
		flex-direction: column;
		justify-content: center;
		align-items: center;
    }
	.item.flex {
		width: 100%;
		padding: 0 20px;
	}
	article {
		width: 43%;
	}
}

section.contactanos{
		padding: 0 20px;
			form{
			width: 100%;
			.row{
				flex-direction: column;
				.col{
					width: 100%;
					margin-right: 0;
				}
			}
		}
	}
	

}
@media only screen and (max-width: 480px) {

}



</style>
