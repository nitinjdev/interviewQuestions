# React Native Interview Question 1: Fetch API and Display Data

## Problem statement

Call a REST API and display the returned movie data in a React Native `FlatList`.

### Points to remember

- **Custom hook:** Keep fetching logic in a reusable `useFetch` hook.
- **Loading and error states:** Show a spinner while loading and a readable message if the request fails.
- **Optimisation:** Use `FlatList` for virtualized rendering and `useCallback` for a stable `renderItem`.
- **Cancellation:** Use `AbortController` to cancel an in-flight request when the URL changes or the component unmounts.
- **Pagination:** Load the next page when the user reaches the end of the list.
- **Filters:** Filter the loaded movies by title or genre.
- **Avoid redundant state:** Derive filtered data from the fetched data instead of copying it into a second state variable.

## Example implementation

> **API note:** Replace the example URL and response mapping with a real API endpoint. This example assumes the API returns `{ "data": [...], "nextPage": 2 }` for a page request. A real API may use a different response shape.

```tsx
import React from 'react';
import {
  ActivityIndicator,
  FlatList,
  StyleSheet,
  Text,
  TextInput,
  View,
} from 'react-native';

type Movie = {
  id: string | number;
  title: string;
  genre?: string;
  imdbRating?: number;
};

type MovieResponse = {
  data: Movie[];
  nextPage?: number | null;
};

function useMovies() {
  const [movies, setMovies] = React.useState<Movie[]>([]);
  const [query, setQuery] = React.useState('');
  const [page, setPage] = React.useState(1);
  const [hasMore, setHasMore] = React.useState(true);
  const [loading, setLoading] = React.useState(false);
  const [refreshing, setRefreshing] = React.useState(false);
  const [error, setError] = React.useState<string | null>(null);

  const loadPage = React.useCallback(
    async (pageToLoad: number, replace = false) => {
      if (loading || (!hasMore && !replace)) return;

      const controller = new AbortController();
      setLoading(true);
      setError(null);

      try {
        const response = await fetch(
          `https://example.com/api/movies?page=${pageToLoad}`,
          { method: 'GET', signal: controller.signal },
        );

        if (!response.ok) {
          throw new Error(`HTTP error: ${response.status}`);
        }

        const result: MovieResponse = await response.json();
        const incoming = Array.isArray(result.data) ? result.data : [];

        setMovies((previous) => {
          if (replace) return incoming;

          // Prevent duplicate items when pages overlap.
          const existingIds = new Set(previous.map((movie) => String(movie.id)));
          return [...previous, ...incoming.filter(
            (movie) => !existingIds.has(String(movie.id)),
          )];
        });

        setPage(pageToLoad);
        setHasMore(
          result.nextPage != null
            ? true
            : incoming.length > 0,
        );
      } catch (caught) {
        const err = caught as Error;
        if (err.name !== 'AbortError') {
          setError(err.message || 'Something went wrong.');
        }
      } finally {
        setLoading(false);
        setRefreshing(false);
      }

      return () => controller.abort();
    },
    [hasMore, loading],
  );

  React.useEffect(() => {
    loadPage(1, true);
  }, []); // Initial request only.

  const onRefresh = React.useCallback(() => {
    setRefreshing(true);
    setHasMore(true);
    loadPage(1, true);
  }, [loadPage]);

  const loadMore = React.useCallback(() => {
    if (!loading && hasMore) {
      loadPage(page + 1);
    }
  }, [hasMore, loadPage, loading, page]);

  const filteredMovies = React.useMemo(() => {
    const normalizedQuery = query.trim().toLowerCase();
    if (!normalizedQuery) return movies;

    return movies.filter((movie) =>
      `${movie.title} ${movie.genre ?? ''}`
        .toLowerCase()
        .includes(normalizedQuery),
    );
  }, [movies, query]);

  return {
    movies: filteredMovies,
    query,
    setQuery,
    loading,
    refreshing,
    error,
    loadMore,
    onRefresh,
  };
}

export default function App() {
  const {
    movies,
    query,
    setQuery,
    loading,
    refreshing,
    error,
    loadMore,
    onRefresh,
  } = useMovies();

  const renderItem = React.useCallback(
    ({ item }: { item: Movie }) => (
      <View style={styles.card}>
        <Text style={styles.title}>{item.title}</Text>
        <Text>Genre: {item.genre ?? 'Unknown'}</Text>
        <Text>IMDb rating: {item.imdbRating ?? 'N/A'}</Text>
      </View>
    ),
    [],
  );

  return (
    <View style={styles.container}>
      <Text style={styles.heading}>Movie List</Text>

      <TextInput
        value={query}
        onChangeText={setQuery}
        placeholder="Filter by title or genre"
        accessibilityLabel="Filter movies"
        style={styles.search}
      />

      {error ? <Text style={styles.error}>Error: {error}</Text> : null}

      <FlatList
        data={movies}
        keyExtractor={(item) => String(item.id)}
        renderItem={renderItem}
        onEndReached={loadMore}
        onEndReachedThreshold={0.5}
        refreshing={refreshing}
        onRefresh={onRefresh}
        ListEmptyComponent={
          loading ? null : <Text>No movies found.</Text>
        }
        ListFooterComponent={
          loading ? <ActivityIndicator style={styles.loader} /> : null
        }
        initialNumToRender={10}
        windowSize={5}
        removeClippedSubviews
      />
    </View>
  );
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    backgroundColor: '#ecf0f1',
    padding: 12,
  },
  heading: {
    fontSize: 20,
    fontWeight: '700',
    marginBottom: 12,
  },
  search: {
    borderWidth: 1,
    borderColor: '#aaa',
    borderRadius: 8,
    paddingHorizontal: 12,
    paddingVertical: 10,
    marginBottom: 12,
    backgroundColor: '#fff',
  },
  card: {
    marginBottom: 8,
    borderColor: '#ccc',
    borderWidth: 1,
    padding: 12,
    borderRadius: 8,
    backgroundColor: '#fff',
  },
  title: {
    fontSize: 16,
    fontWeight: '600',
    marginBottom: 4,
  },
  error: {
    color: 'red',
    marginBottom: 8,
  },
  loader: {
    padding: 12,
  },
});
```

## Important implementation notes

The code above is an interview-oriented outline; for production, make cancellation and pagination race-safe. In particular, a robust custom hook should keep the active `AbortController` in a ref, abort it in an effect cleanup, and use a request ID or ref-based loading lock to prevent overlapping `onEndReached` calls. The sample's `loadPage` return value does not automatically become an effect cleanup, and `hasMore` should ideally be based on the API's actual pagination metadata (`nextPage`, `totalPages`, or a cursor), not only on whether a page contains items.

## Interview discussion

### Why use a custom hook?
It separates API and state-management logic from UI rendering, improves reuse, and makes the component easier to test.

### Why use `FlatList` instead of `ScrollView`?
`FlatList` virtualizes rows and renders a window of items, which is better for long lists.

### Why use `useCallback`?
It can preserve the `renderItem` function reference between renders. It is most useful when stable props help avoid unnecessary child renders; it is not automatically a performance improvement in every case.

### Why use `useMemo` for filtering?
It avoids recalculating the filtered list unless the movies or query change. For a very small list, the extra memoization may not be necessary.

### How should pagination work?
Use the API's documented page/cursor fields, append results, deduplicate by ID if needed, and prevent duplicate requests while a page is loading.

### How should filters work?
For client-side filtering, filter the items already loaded. If the dataset is large, prefer server-side filtering and reset pagination when the filter changes.

## Checklist

- [ ] API call and response mapping
- [ ] Loading, empty, and error states
- [ ] Abort/cancellation handling
- [ ] `FlatList` and stable keys
- [ ] Pagination and duplicate-request prevention
- [ ] Search/filter by title or genre
- [ ] Pull-to-refresh
- [ ] TypeScript types
- [ ] Server-side filtering for large datasets
