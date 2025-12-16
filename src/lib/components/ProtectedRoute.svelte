<script lang="ts">
	import { onMount } from 'svelte';
	import { goto } from '$app/navigation';
	import { isAuthenticated, authLoading } from '$lib/stores/auth';

	export let redirectTo = '/login';

	onMount(() => {
		// Wait for auth to initialize
		const unsubscribe = authLoading.subscribe((loading) => {
			if (!loading && !$isAuthenticated) {
				goto(redirectTo);
			}
		});

		return unsubscribe;
	});
</script>

{#if $authLoading}
	<div class="flex min-h-screen items-center justify-center">
		<div class="text-lg">Loading...</div>
	</div>
{:else if $isAuthenticated}
	<slot />
{:else}
	<div class="flex min-h-screen items-center justify-center">
		<div class="text-lg">Redirecting...</div>
	</div>
{/if}
