<script context="module" lang="ts">
    import type { Load } from '@sveltejs/kit';
    import { stringify } from '$lib/utils';
  
    export const load: Load = async ({ fetch, page: { params, query } }) => {
        const formData = stringify({
            q: params.q,
            type: query.get('type') ?? 'all',
            category: query.get('category') ?? '',
            order: query.get('order') ?? 'popular'
        });

        const res = await fetch(`/api/search?${formData}`);
  
        if (res.ok) {
            const data = await res.json();
            return {
                props: {
                    hits: data.data.hits
                }
            }
        }
  
        return {
            status: res.status,
            error: new Error()
        }
    }
</script>

<script lang="ts">
import type { Image } from '$lib/types'
import ImageCard from '../../components/imageCard.svelte';

export let hits: Image[] = [];
</script>

<div class="grid gap-4 grid-cols-1 md:grid-cols-2 lg:grid-cols-3">
    {#each hits as image}
        <ImageCard image={image} />
    {/each}
</div>