<script>
	import { isLogged, load_page_index } from './get_stores.mjs'
	import { is_logged, page_index, user_data } from './stores.mjs';
	import Navbar from './components/Navbar.svelte';
	import './static/loginstyle.css';
    
	// REMOTE_SERVER is defined in rollup.config.js
	const url = "REMOTE_SERVER";

	let username = "GUEST";
	let email;
	let secretKey;
	let per_case_complaints = [];
	let complaints = [{
		_id: "7b29bd67-482e-4127-b6f9-a907cdf2caea",
		title:"Komşumda şüheli bir gal arısı durumu var",
		content: "Eklerde mevcuttur",
		comp_user: "59e5321b-b43c-48b2-883a-42ff46dfe49e",
		complainant: "3446d7fd-b74b-4b2d-909b-1dd0e7b4c8d4"
	}, {
		_id: "8ffe7d06-06ff-4d97-b59f-f03224bac654",
		title:"şüheli bir gal arısı durumu var ve bildirilmiyor!",
		content: "Bir göz atılması şart",
		comp_user: "5321532sdf-c8b2-883a-42ff46dfe49e",
		complainant: "344dxt345-4b2d-909b-1dd0e7b4c8d4"
	}, {
		_id: "c82a2c8c-7497-4ce6-8893-2cffdf581db8",
		title:"şüheli bir gal arısı durumu var ve bildirilmiyor!",
		content: "Ne yapılmıası gerekiyorsa acil yapılımalı. Lütfen birimleri yollayın.",
		comp_user: "673s5321b-b43c-48b2-883a-42ff46dfe49e",
		complainant: "3446d7fd-b74b-4b2d-909b-1dd0e7b4c8d4"
	}, {
		_id: "f75ba1cc-ee20-44a7-ae24-f457bb946d15"
	}];

	// for frotend
	let zero_to_n = [];
	let br_limit = 12
	for(let i = 0; i <= br_limit; i++)
	{
		zero_to_n.push(i)
	}

	function get_entries(_case)
	{
		const result = [];
		for(const spec of _case)
		{
			result.push(spec);
		}
	}

	async function process_complaint_case_data(comp_id)
	{
		// EXPRESS_PORT IS DEFINED IN rollup.config.js
		const data = await fetch(`${url}/complaint_case`, {
			method: "POST",
			body: JSON.stringify({
				comp_id: comp_id
			}),
			headers: {
				"Content-Type": "application/json",
				'Access-Control-Allow-Methods': 'OPTIONS,POST',
				'Access-Control-Allow-Credentials': true,
				'Access-Control-Allow-Origin': '*',
				'X-Requested-With': '*',
			}
		})

		const response = await data.json();
		const result = [];
		if(response.status === 200)
		{
			for(const x of response.data)
			{
				result.push(x);
			}

			return result;
		}
	}
	
	async function submit_form(email, secretKey)
	{
		// EXPRESS_PORT IS DEFINED IN rollup.config.js
		const data = await fetch(`${url}/login`, {
			method: "POST",
			body: JSON.stringify({
				email: email,
				secretKey: secretKey
			}),
			headers: {
			"Content-Type": "application/json",
			'Access-Control-Allow-Methods': 'OPTIONS,POST',
			'Access-Control-Allow-Credentials': true,
			'Access-Control-Allow-Origin': '*',
			'X-Requested-With': '*',
			}
		})
		const result = await data.json()

		if (result.response === 200)
		{
			// set stores!
			user_data.set(result.user_data);
			is_logged.set(true)
			page_index.set(1)
			// set variables
			username = result.user_data.username;
			logged = true;
			ui = 1;
			window.alert(`Hoşgeldiniz, ${username}!\nSisteme erişim veriliyor...`)
		} else if(result.response === 403){
			window.alert('ADMIN OLMADIĞINIZ HALDE SİSTEME ERİŞİM\nSAĞLAMAYA ÇALIŞTIĞINIZDAN BANLANIYORSUNUZ.')
			setTimeout(() => window.close(), 2000)
		} else {
			window.alert('GİRİŞ BAŞARISIZ!!!')
		}
	}

	async function get_complaints(email, secretKey)
		{
		// EXPRESS_PORT IS DEFINED IN rollup.config.js
		const data = await fetch(`${url}/login`, {
			method: "POST",
			body: JSON.stringify({
				email: email,
				secretKey: secretKey
			}),
			headers: {
			"Content-Type": "application/json",
			'Access-Control-Allow-Methods': 'OPTIONS,POST',
			'Access-Control-Allow-Credentials': true,
			'Access-Control-Allow-Origin': '*',
			'X-Requested-With': '*',
			}
		});

		const result = await data.json();

		if (result.response === 200)
		{
			complaints = result.data
		} else {
			complaints = "ERROR_CANNOT_GET_COMPLAINTS";
			console.error(complaints)
		}
	}

	const send_form = async () => {
		await submit_form(email, secretKey);

		await get_complaints(email, secretKey)
		
		setTimeout(() => console.log(complaints), 4500);
	}

	// reactives
	let logged_reactive_component = true ?? isLogged();
	$: logged = logged_reactive_component ?? false;

	let ui_reactive_component = 1 ?? load_page_index();
	$: ui = ui_reactive_component ?? 0;

	let title_list = ["Giriş Sayfası", "Ana Sayfa / Şikaye Değerlendirme"];
	$: title_reactive = title_list[ui]
</script>
<svelte:head>
	<title>Gal-BUL Masaüstü İstemcisi (Admin Panel) | {title_reactive ?? "Ana Sayfa"}</title>
</svelte:head>
<div class="container" style={`${!logged ? "overflow:hidden;" : "overflow:hidden;"}`}>
	<div class="navbar" style="position:absolute; top: -8.1%; left: 0%">
		<Navbar logged={logged}/>
	</div>
	{#if ui === 0}
		<main style="overflow: hidden;">
			<div class="login-box" style="position: absolute; top: 50%; left: 50%;">
				<h2>Admin Panel Giriş Formu</h2>
				<br/>
				<form on:submit|preventDefault={send_form} method="POST" name="loginform" autocomplete="off">
					<div class="user-box">
						<input bind:value={email} type="email" name="email" placeholder="Kullanıcı E-mail" required="">
					</div>
					<div class="user-box">
						<input bind:value={secretKey} type="password" name="password" placeholder="Günlük değişen gizli anahtar..." required="">
					</div>
					<!-- svelte-ignore a11y-invalid-attribute -->
					<a href="#">
						<span></span>
						<span></span>
						<span></span>
						<span></span>
						<input type="submit" value="Giriş yap!"/>
					</a>
				</form>
			</div>
		</main>
	{:else if ui === 1 && logged}
		<main>
			<center>
				<h1>
					<br>
					<u class="nosecet text-white" style="padding-top: 2.5rem;font-family:monospace; font-size: 2.5rem; color: white;">Şikayet Değerlendirme:</u>
				</h1>
				<div style="margin-bottom: 50%;"></div>
				<div class="complaints">
				{#each complaints as single_case}
					<div class=" reset-thisnoselect text-white complaint login-box" style="text-align:start; position: relative; left: 20%;">
						<div class="reset-this text-white" style="color:white; margin-bottom: 10px; text-overflow:ellipsis;">
							ID: {single_case._id}
						</div>
						<br>
						<div class=" reset-this text-white" style="overflow: hidden; color:white; text-overflow:ellipsis;"> 
							Şikayet edilen: <a style="text-decoration: underline;" href={`http://localhost:8081/profile/${single_case.comp_user}`}>{single_case.comp_user}</a>
						</div>
						<br>
						<div class="text-white" style="color:white; text-overflow:ellipsis;">
							Şikayet başlığı: {single_case.title}
						</div>
						<br>
						<div class="text-white" style="color:white; text-overflow:ellipsis;">
							Şikayet içeriği: {single_case.content}
						</div>
						<br>
						<div class="text-white" style="color:white; text-overflow:ellipsis;">
							Şikayet Ek/Resimi:
							<br>
							<br>
							<br>
							<img style="width: 250px; height: 250px;"src={`http://localhost:8081/static/webimgs/${single_case._id}.png`} alt="proof_of_consept"/>
							<br>
							<br>
							<br>
							Şikayet eden: <a style="text-decoration: underline;" href={`http://localhost:8081/profile/${single_case.complainant}`}>{single_case.complainant}</a>
						</div>
					</div>
					<div style="maring-bottom: 150px;"></div>
				{/each}
				</div>
			</center>
		</main>
	{:else}
		{#each zero_to_n as i}
			<br/>
		{/each}
		<center style="font-family: monospace !important; font-size: 3rem; display:flex; justify-content:center; align-items:center; color:white;">
			404 Hatası: Sayfa Bulunamadı!
		</center>
	{/if}
</div>
<style>
	@media (min-width: 640px) {
		main {
			max-width: none;
		}
	}
</style>
