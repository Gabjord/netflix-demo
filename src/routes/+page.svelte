<script>
	// @ts-nocheck

	import Icon from '@iconify/svelte';
	import { flip } from 'svelte/animate';
	import { quartInOut, quintInOut } from 'svelte/easing';
	import { derived } from 'svelte/store';
	import { fade, fly, slide } from 'svelte/transition';
	let search = $state('');
	// for mobile nav list
	let openMenu = $state(false);
	let cartCount = $derived(cart.length);
	// cart modal
	let cartModal = $state(false);
	// categories cleaned
	let categories = $derived(['All', ...new Set(products.map((p) => p.category))]);
	let total = $derived(cart.reduce((sum, item) => sum + item.salePrice, 0));
	let addedMessage = $state(false);

	let currentIndex = $state(0);

	let products = $state([
		{
			id: crypto.randomUUID(),
			name: 'ProBook X1',
			price: 1200,
			salePrice: 999,
			category: 'Laptop',
			style: 'bg-slate-900 text-white',
			img: '/probook.jpg'
		},
		{
			id: crypto.randomUUID(),
			name: 'SoundWave Z',
			price: 250,
			salePrice: 150,
			category: 'Audio',
			style: 'bg-indigo-950 text-white',
			img: '/SoundWave.jpg'
		},
		{
			id: crypto.randomUUID(),
			name: 'PixelTab 10',
			price: 600,
			salePrice: 450,
			category: 'Tablet',
			style: 'bg-orange-500 text-white',
			img: '/PixelTab.jpg'
		},
		{
			id: crypto.randomUUID(),
			name: 'Vortex GPU',
			price: 800,
			salePrice: 720,
			category: 'PC Parts',
			style: 'bg-zinc-800 text-zinc-100',
			img: '/gpu.jpg'
		},
		{
			id: crypto.randomUUID(),
			name: 'Echo Buds',
			price: 120,
			salePrice: 80,
			category: 'Audio',
			style: 'bg-slate-900 text-white',
			img: '/airpod.jpg'
		},
		{
			id: crypto.randomUUID(),
			name: 'Swift Air 13',
			price: 900,
			salePrice: 850,
			category: 'Laptop',
			style: 'bg-zinc-100 text-zinc-900',
			img: '/swift.jpg'
		},
		{
			id: crypto.randomUUID(),
			name: 'UltraWide 34',
			price: 550,
			salePrice: 400,
			category: 'Monitor',
			style: 'bg-indigo-950 text-white',
			img: '/UltraWide.jpg'
		},
		{
			id: crypto.randomUUID(),
			name: 'Core i9 Gen14',
			price: 580,
			salePrice: 520,
			category: 'PC Parts',
			style: 'bg-slate-900 text-white',
			img: '/i9.jpg'
		},
		{
			id: crypto.randomUUID(),
			name: 'Neo Watch',
			price: 300,
			salePrice: 199,
			category: 'Wearable',
			style: 'bg-orange-500 text-white',
			img: '/neo.jpg'
		},
		{
			id: crypto.randomUUID(),
			name: 'Iphone 17',
			price: 1100,
			salePrice: 950,
			category: 'Mobile',
			style: 'bg-zinc-900 text-zinc-100',
			img: '/iphone17.jpg'
		},
		{
			id: crypto.randomUUID(),
			name: 'Gamer Max',
			price: 2100,
			salePrice: 1800,
			category: 'Laptop',
			style: 'bg-indigo-950 text-white',
			img: '/gamerMax.jpg'
		},
		{
			id: crypto.randomUUID(),
			name: 'Pulse Fit',
			price: 150,
			salePrice: 99,
			category: 'Wearable',
			style: 'bg-orange-500 text-white',
			img: '/pulsefit.jpg'
		},
		{
			id: crypto.randomUUID(),
			name: 'Titan SSD 2TB',
			price: 200,
			salePrice: 160,
			category: 'PC Parts',
			style: 'bg-zinc-800 text-zinc-100',
			img: '/ssd.jpg'
		},
		{
			id: crypto.randomUUID(),
			name: 'Cinema View 4K',
			price: 1500,
			salePrice: 1200,
			category: 'Monitor',
			style: 'bg-slate-900 text-white',
			img: '/4kMonitor.jpg'
		},
		{
			id: crypto.randomUUID(),
			name: 'KeyBoard Mech 1',
			price: 150,
			salePrice: 120,
			category: 'PC Parts',
			style: 'bg-zinc-100 text-zinc-900',
			img: '/keyboard.jpg'
		},
		{
			id: crypto.randomUUID(),
			name: 'Focus Cam',
			price: 400,
			salePrice: 350,
			category: 'Camera',
			style: 'bg-indigo-950 text-white',
			img: '/camera.jpg'
		},
		{
			id: crypto.randomUUID(),
			name: 'Hyper Mouse',
			price: 80,
			salePrice: 45,
			category: 'PC Parts',
			style: 'bg-orange-500 text-white',
			img: '/mouse.jpg'
		},
		{
			id: crypto.randomUUID(),
			name: 'Cloud Pad',
			price: 250,
			salePrice: 180,
			category: 'Tablet',
			style: 'bg-zinc-800 text-zinc-100',
			img: '/cloudPad.jpg'
		},
		{
			id: crypto.randomUUID(),
			name: 'Aero Drone',
			price: 900,
			salePrice: 750,
			category: 'Gadgets',
			style: 'bg-slate-900 text-white',
			img: '/drone.jpg'
		},
		{
			id: crypto.randomUUID(),
			name: 'Sonic Bar',
			price: 350,
			salePrice: 280,
			category: 'Audio',
			style: 'bg-indigo-950 text-white',
			img: '/sonicBar.jpg'
		}
	]);

	let selectedCat = $state('All'); //cart
	let cart = $state([]); // filtered products

	let filteredProducts = $derived(
		products
			.filter((p) => selectedCat === 'All' || p.category === selectedCat)
			.filter((p) => p.name.toLowerCase().includes(search.toLowerCase()))
	);

	function toggleCart(item) {
		const exists = cart.some((i) => i.id === item.id);

		if (exists) {
			cart = cart.filter((i) => i.id !== item.id);
			addedMessage = false;
		} else {
			addedMessage = true;
			cart = [...cart, item];

			setTimeout(() => (addedMessage = false), 2000);
		}
	}
	/*$effect(() => {
		const mixer = setInterval(() => currentIndex + (1 % categories.length), 5000);
		return () => clearInterval(mixer);
	});*/
</script>

<main class="relative min-h-screen overflow-hidden bg-gray-50 pb-3 font-sans text-gray-900">
	<header
		class=" sticky top-0 z-10 flex h-20 items-center justify-between gap-2 bg-white px-3 md:px-10 lg:justify-evenly"
	>
		<h1 class="text-2xl font-extrabold text-nowrap text-orange-600 md:flex-1">ELECTRO-SALE</h1>
		<div class=" hidden flex-3 md:block">
			<input
				type="text"
				class="mx-auto block w-[40%] rounded-lg bg-orange-400 py-2 text-center text-white placeholder-gray-100 outline-blue-400"
				bind:value={search}
				placeholder="Search"
			/>
		</div>

		<!-- menu -->
		{#if openMenu === false}
			<div class="block cursor-pointer md:hidden" transition:slide>
				<Icon
					onclick={() => (openMenu = !openMenu)}
					icon="lucide:menu"
					class="text-orange-500"
					width="24"
					height="24"
				/>
			</div>
		{:else}
			<div
				class="flex cursor-pointer gap-3"
				transition:slide={{ duration: 600, easing: quintInOut }}
			>
				<nav class=" flex list-none gap-4 text-xl font-semibold">
					<li class="hover:text-orange-500">Home</li>
					<li class="hover:text-orange-500">About</li>
					<li class="hover:text-orange-500">Cart</li>
				</nav>
				<Icon
					icon="line-md:menu-to-close-transition"
					width="30"
					onclick={() => (openMenu = !openMenu)}
				/>
			</div>
		{/if}
		<div class="hidden gap-1 lg:flex">
			<button
				title="Cart button"
				onclick={() => (cartModal = !cartModal)}
				class="cursor-pointer text-2xl font-bold">🛒 Cart:</button
			>
			<p class=" flex-1 rounded-full bg-orange-400 px-1.5 py-0.5 text-center text-white">
				{cartCount}
			</p>
		</div>
	</header>
	<!-- hero section -->
	<section
		class=" flex h-64 w-full flex-col items-center justify-center space-y-2 bg-indigo-950 bg-[url('discount.jpg')] bg-cover bg-center bg-no-repeat text-center text-balance text-white"
	>
		<h1 class="font-serif text-3xl font-extrabold text-white/90 md:text-6xl md:text-orange-950">
			Flash Sales Events
		</h1>
		<p class="text-xl font-semibold text-red-600 md:text-lg md:text-orange-700">
			Up to 40% off On Premium electronics
		</p>
	</section>

	<!-- category section -->
	<section
		class="flex h-20 items-center gap-3 overflow-x-scroll overflow-y-hidden text-nowrap md:w-full lg:mx-auto lg:w-[85%] lg:justify-center lg:overflow-x-hidden"
	>
		{#each categories as cat}
			<button
				class={`  h-fit  cursor-pointer  py-2 rounded-full px-4 text-lg font-medium shadow-xs shadow-gray-500 ${selectedCat === cat ? 'bg-orange-500 text-white' : 'text-gray-950'}`}
				onclick={() => (selectedCat = cat)}
			>
				{cat}
			</button>
		{/each}
	</section>
	<!-- display product -->
	<section
		class="relative mt-5 grid w-full list-none grid-cols-1 place-items-center content-center gap-4 px-3 sm:grid-cols-2 lg:grid-cols-4"
	>
		<!-- added message successful -->
		{#if addedMessage}
			<div
				class=" fixed top-1/2 left-1/2 z-80 -translate-1/2 rounded-md p-4 text-lg font-normal text-white"
			>
				<p class="t w-fit rounded-md bg-orange-500 px-4 py-1.5">Added ✔</p>
			</div>
		{/if}
		<!-- floating icon -->
		{#if cart.length > 0}
			<section
				transition:slide={{ easing: quartInOut, duration: 600 }}
				class="fixed right-6 bottom-5 w-fit animate-bounce cursor-pointer rounded bg-indigo-950 p-4 transition-all duration-500 hover:bg-orange-500"
			>
				<Icon
					icon="lucide:shopping-cart"
					class="text-2xl  text-white "
					onclick={() => (cartModal = !cartModal)}
				/>
				<p class="absolute top-0 right-0 text-2xl text-white">{cartCount}</p>
			</section>
		{/if}

		{#if filteredProducts.length > 0}
			{#each filteredProducts as product (product.id)}
				<div
					class="h-85 w-full overflow-hidden rounded-lg border md:w-80"
					animate:flip={{ duration: 600, easing: quintInOut }}
					in:fly={{ y: 30, duration: 500, easing: quintInOut }}
					out:fade={{ duration: 300 }}
				>
					<li class={`${product.style} flex h-1/2 min-w-full `}>
						<img src={product.img} alt={product.name} class="h-full w-full object-cover" />
					</li>
					<!-- listing -->
					<div class={` flex flex-col gap-3  pl-3`}>
						<p class="text-lg font-bold text-orange-500">{product.category}</p>
						<p class="text-xl font-semibold text-indigo-950">{product.name}</p>
						<p class="text-2xl font-semibold text-indigo-950">
							${product.salePrice}
							<span class="text-2xl font-semibold text-gray-400 line-through opacity-40"
								>${product.price}</span
							>
						</p>
						<button
							onclick={() => {
								toggleCart(product);
							}}
							class={` mx-auto  w-[90%] cursor-pointer rounded-lg bg-indigo-950 px-4 py-2 ${cart.some((item) => item.id === product.id) ? 'bg-orange-500 text-white' : 'border-black'} text-white hover:bg-orange-500`}
							>{cart.some((item) => item.id === product.id) ? 'Added' : 'Add to Cart'}</button
						>
					</div>
				</div>
			{/each}
		{:else}
			<p class="w-screen text-center text-4xl font-semibold text-red-500">No Match Found😥</p>
		{/if}
	</section>

	<!-- cart overlay -->
	{#if cartModal}
		<!-- cart header -->
		<div
			transition:fade={{ duration: 200 }}
			class="fixed inset-0 z-7 flex items-center justify-center bg-slate-950/60 p-4 backdrop-blur-md"
		>
			<div
				transition:fly={{ y: 20, duration: 400 }}
				class="relative w-full max-w-lg overflow-hidden rounded-2xl bg-white shadow-2xl"
			>
				<div
					class="flex items-center justify-between border-b border-gray-100 bg-gray-50/50 px-6 py-4"
				>
					<div class="flex items-center gap-2">
						<Icon icon="lucide:shopping-cart" class="text-orange-600" width="22" />
						<h2 class="text-xl font-bold text-slate-800">Your Cart</h2>
						<span
							class="ml-2 rounded-full bg-orange-100 px-2.5 py-0.5 text-sm font-bold text-orange-600"
						>
							{cart.length}
						</span>
					</div>
					<button
						onclick={() => (cartModal = false)}
						class="rounded-full p-1 text-gray-400 transition-colors hover:bg-gray-200 hover:text-gray-900"
					>
						<Icon icon="line-md:menu-to-close-transition" width="24" class="cursor-pointer" />
					</button>
				</div>
				<!-- items  -->
				<div class=" max-h-[60vh] overflow-y-auto p-6">
					{#if cart.length > 0}
						<div class="space-y-4">
							{#each cart as item (item.id)}
								<div
									transition:slide
									class="flex items-center gap-4 rounded-lg border border-gray-100 p-3 hover:bg-gray-50"
								>
									<img src={item.img} alt={item.name} class="h-16 w-16 rounded-md object-cover" />
									<div class="flex-1">
										<p class="font-bold text-slate-800">{item.name}</p>
										<p class="text-sm font-semibold text-orange-600">${item.salePrice}</p>
									</div>
									<button
										onclick={() => toggleCart(item)}
										class="text-gray-400 transition-colors hover:text-red-500"
									>
										<Icon icon="lucide:trash-2" width="20" class="cursor-pointer" />
									</button>
								</div>
							{/each}
						</div>
					{:else}
						<div class="flex flex-col items-center justify-center py-10 text-center">
							<Icon icon="lucide:shopping-bag" class="mb-4 text-gray-200" width="64" />
							<p class="text-lg font-medium text-gray-400">Your cart is empty</p>
						</div>
					{/if}
				</div>
				<!-- total and checkout -->
				{#if cart.length > 0}
					<div class="border-t border-gray-100 bg-white p-6">
						<div class="mb-4 flex justify-between text-lg font-bold text-slate-900">
							<span>Total</span> <span>${total}</span>
						</div>
						<button
							class="w-full rounded-xl bg-orange-500 py-3 font-bold text-white shadow-lg shadow-orange-200 transition-transform hover:scale-[1.02] active:scale-95"
						>
							Checkout Now
						</button>
					</div>
				{/if}
			</div>
		</div>
	{/if}
</main>
