<script lang="ts">
	import { onMount } from 'svelte';
	import { LogOut } from '@lucide/svelte';
	import { logout } from '$lib/stores/auth';
	import { showToast } from '$lib/stores/toast';

	export let name = 'User';
	export let email = '';
	export let avatar = '';

	let showMenu = false;
	let menuRef: HTMLDivElement | null = null;

	$: initial = (name || 'U').slice(0, 1).toUpperCase();

	function toggleMenu() {
		showMenu = !showMenu;
	}

	function closeMenu() {
		showMenu = false;
	}

	onMount(() => {
		const handleClick = (event: MouseEvent) => {
			if (showMenu && menuRef && !menuRef.contains(event.target as Node)) {
				closeMenu();
			}
		};

		document.addEventListener('click', handleClick);

		return () => {
			document.removeEventListener('click', handleClick);
		};
	});

	async function handleLogout() {
		try {
			await logout();
			closeMenu();
			showToast('Logged out', 'success');
		} catch (error) {
			console.error('Logout failed', error);
			showToast('Failed to log out', 'error');
		}
	}
</script>

<div class="relative" bind:this={menuRef}>
	<button
		type="button"
		class="grid h-8 w-8 place-items-center rounded-full border border-slate-200 bg-white shadow-sm transition hover:-translate-y-0.5 hover:shadow focus-visible:ring-2 focus-visible:ring-slate-400 focus-visible:outline-none"
		on:click={toggleMenu}
		aria-haspopup="menu"
		aria-expanded={showMenu}
		aria-label="Open profile menu"
	>
		{#if avatar}
			<img
				src={avatar}
				alt={name}
				class="h-7 w-7 rounded-full object-cover"
				referrerpolicy="no-referrer"
			/>
		{:else}
			<div
				class="flex h-7 w-7 items-center justify-center rounded-full bg-slate-900 text-sm font-semibold text-white"
			>
				{initial}
			</div>
		{/if}
	</button>

	{#if showMenu}
		<div
			class="absolute left-0 z-10 mt-2 w-64 overflow-hidden rounded-2xl border border-slate-200 bg-white shadow-xl ring-1 ring-black/5"
		>
			<div class="flex items-center gap-3 px-4 py-3">
				{#if avatar}
					<img
						src={avatar}
						alt={name}
						class="h-7 w-7 rounded-full object-cover"
						referrerpolicy="no-referrer"
					/>
				{:else}
					<div
						class="flex h-7 w-7 items-center justify-center rounded-full bg-slate-900 text-sm font-semibold text-white"
					>
						{initial}
					</div>
				{/if}
				<div class="min-w-0">
					<p class="text-sm font-semibold text-slate-900">{name}</p>
					{#if email}
						<p class="truncate text-xs text-slate-500">{email}</p>
					{/if}
				</div>
			</div>
			<button
				type="button"
				class="flex w-full items-center gap-2 px-4 py-3 text-sm font-semibold text-slate-700 transition hover:bg-slate-50"
				on:click={handleLogout}
			>
				<LogOut class="h-4 w-4" />
				Log out
			</button>
		</div>
	{/if}
</div>
