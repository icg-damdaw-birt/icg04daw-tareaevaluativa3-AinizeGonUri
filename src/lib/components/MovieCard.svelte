<script lang="ts">
  import type { Movie } from '$lib/types';

  // Props con Svelte 5: sistema de tipos explícito y callbacks en lugar de eventos
  let { 
    movie,
    showActions = true,
    ondelete,
    onedit,
    onfavorite,
    onrate
  }: {
    movie: Movie;
    showActions?: boolean;
    ondelete?: (id: string) => void;
    onedit?: (movie: Movie) => void;
    onfavorite?: (id: string) => void;
    onrate?: (id: string, rating: number) => void;
  } = $props();

  // Handlers: ejecutan callbacks del padre directamente
  function handleDelete() {
    ondelete?.(movie.id);
  }

  function handleEdit() {
    onedit?.(movie);
  }

  function handleFavorite() {
    onfavorite?.(movie.id);
  }

  function handleRate(rating: number) {
    onrate?.(movie.id, rating);
  }
</script>

<!-- Componente reutilizable: tarjeta para mostrar información de una película -->
<article class="flex flex-col overflow-hidden rounded-lg border border-slate-200 bg-white shadow-sm">
  {#if movie.posterUrl}
    <div class="flex h-48 items-center justify-center bg-slate-100">
      <img
        alt={`Póster de ${movie.title}`}
        class="max-h-full max-w-full object-contain"
        src={movie.posterUrl}
        loading="lazy"
      />
    </div>
  {/if}

  <div class="flex flex-1 flex-col gap-3 p-4">
    <div class="flex items-start justify-between gap-2">
      <header class="flex-1">
        <h3 class="text-lg font-semibold text-slate-900">{movie.title}</h3>
        <p class="text-sm text-slate-600">Dirigida por {movie.director}</p>
      </header>
      {#if showActions}
        <button
          type="button"
          class="mt-1 flex-shrink-0 rounded-full p-1 transition {movie.isFavorite ? 'text-red-500 hover:text-red-600' : 'text-slate-400 hover:text-slate-600'}"
          title={movie.isFavorite ? 'Quitar de favoritos' : 'Añadir a favoritos'}
          onclick={handleFavorite}
        >
          <span class="text-2xl">{movie.isFavorite ? '♥' : '♡'}</span>
        </button>
      {/if}
    </div>

    <div class="flex items-center justify-between gap-2 text-sm text-slate-500">
      <div>
        {#if movie.year}
          <span>Año: {movie.year}</span>
        {/if}
      </div>
      {#if showActions && movie.rating !== undefined}
        <div class="flex gap-1">
          {#each [1, 2, 3, 4, 5] as star (star)}
            <button
              type="button"
              class="transition hover:scale-110 {star <= (movie.rating ?? 0) ? 'text-yellow-400' : 'text-slate-300'}"
              title="Calificar con {star} estrella{star > 1 ? 's' : ''}"
              onclick={() => handleRate(star)}
            >
              ★
            </button>
          {/each}
        </div>
      {/if}
    </div>

    {#if showActions}
      <div class="mt-3 flex flex-col gap-2 sm:flex-row">
        <button
          type="button"
          class="flex-1 rounded border border-slate-300 px-3 py-2 text-slate-700 transition hover:bg-slate-50"
          onclick={handleEdit}
        >
          Editar
        </button>
        <button
          type="button"
          class="flex-1 rounded border border-red-500 px-3 py-2 text-red-600 transition hover:bg-red-50"
          onclick={handleDelete}
        >
          Eliminar
        </button>
      </div>
    {/if}
  </div>
</article>
