# MoviesDatabase API Documentation

## API Overview

MoviesDatabase is a comprehensive API providing access to extensive entertainment data including movies, TV shows, actors, and related metadata. The service offers:

- Complete and updated data for over 9 million titles (movies, series, and episodes)
- Information for over 11 million actors, crew, and cast members
- Rich data including YouTube trailer URLs, awards, biographies, ratings, and technical specifications
- Weekly updates for recent titles and daily updates for ratings/episode information
- Support for multiple query parameters and flexible data retrieval options

The API is maintained by independent developers who accept support through Buy Me a Coffee.

## API Version

Current version: Not explicitly versioned in documentation (latest available as of documentation review)

## Available Endpoints

### Titles Endpoints
- **GET /titles** - Returns array of titles based on filters/sorting parameters
- **GET /x/titles-by-ids** - Returns specific titles by ID list
- **GET /titles/{id}** - Returns detailed information for a specific title by IMDb ID
- **GET /titles/{id}/ratings** - Returns rating and votes for a specific title
- **GET /titles/series/{id}** - Returns episodes for a series in ascending order
- **GET /titles/seasons/{id}** - Returns number of seasons for a series
- **GET /titles/series/{id}/{season}** - Returns episodes for a specific season
- **GET /titles/episode/{id}** - Returns detailed information for a specific episode
- **GET /titles/x/upcoming** - Returns upcoming titles

### Search Endpoints
- **GET /titles/search/keyword/{keyword}** - Search titles by keyword
- **GET /titles/search/title/{title}** - Search titles by title (with exact match option)
- **GET /titles/search/akas/{aka}** - Search titles by alternative titles (exact match only)

### Actors Endpoints
- **GET /actors** - Returns array of actors based on filters
- **GET /actors/{id}** - Returns detailed information for a specific actor by IMDb ID

### Utility Endpoints
- **GET /title/utils/titleType** - Returns array of available title types
- **GET /title/utils/genres** - Returns array of available genres
- **GET /title/utils/lists** - Returns array of available title lists for querying

## Request and Response Format

### Request Structure
All query parameters are optional. Common parameters include:

- `info` - Specifies data detail level (mini_info, base_info, image, etc.)
- `limit` - Number of results per page (default: 10, max: 50)
- `page` - Page number for pagination
- `titleType` - Filters by type (movie, series, etc.)
- `year`, `startYear`, `endYear` - Year-based filtering
- `genre` - Genre filtering (case-sensitive)
- `sort` - Sorting options (year.incr, year.decr)
- `list` - Predefined lists (most_pop_movies, top_rated_250, etc.)

### Response Structure
All endpoints return objects with a `results` key. Paginated endpoints include additional keys:
- `page` - Current page number
- `next` - Next page identifier
- `entries` - Total number of entries

### Example Response
```json
{
  "results": [
    {
      "id": "tt0000270",
      "titleText": "Example Title",
      "primaryImage": {...},
      "titleType": {...},
      "releaseYear": {...}
    }
  ],
  "page": 1,
  "next": "page_2",
  "entries": 1000
}
```

## Authentication

The MoviesDatabase API documentation **does not mention authentication requirements**. Based on the provided documentation:

- No API key or authentication headers appear to be required
- Requests seem to be publicly accessible
- No authentication mechanism is described in endpoints or parameters

*Note: Always verify current authentication requirements as API policies may change.*

## Error Handling

While specific error codes are not detailed in the documentation, typical API error handling should include:

### Common Error Scenarios
- **Invalid ID Format** - When providing malformed IMDb IDs
- **Non-existent Resources** - When querying for IDs that don't exist
- **Parameter Validation Errors** - When using incompatible parameters together
- **Rate Limit Exceeded** - When exceeding request limits

### Recommended Handling
```typescript
try {
  const response = await fetch(apiUrl);
  if (!response.ok) {
    switch(response.status) {
      case 404:
        console.error('Resource not found');
        break;
      case 400:
        console.error('Bad request - check parameters');
        break;
      default:
        console.error(`API error: ${response.status}`);
    }
  }
  const data = await response.json();
} catch (error) {
  console.error('Network error:', error);
}
```

## Usage Limits and Best Practices

### Known Limitations
- **Maximum limit per request**: 50 results
- **Top lists limitations**: Some lists have fixed sizes (e.g., top_rated_250, top_boxoffice_200)
- **Exact matching requirements**: Some endpoints require exact, case-sensitive matches

### Best Practices
1. **Use Specific Info Parameters**: Request only needed data using the `info` parameter to reduce payload size
2. **Implement Pagination**: Use `limit` and `page` parameters for large datasets
3. **Cache Responses**: Cache frequently accessed data to reduce API calls
4. **Handle Rate Limits**: Implement exponential backoff for retry logic
5. **Validate Parameters**: Ensure year ranges and genre filters use correct formats
6. **Use Predefined Lists**: Leverage existing lists (like `most_pop_movies`) for optimized queries
7. **Check Update Frequency**: Remember that ratings update daily while titles update weekly
8. **Support the Developers**: Consider supporting via Buy Me a Coffee for continued service

### Performance Tips
- Start with `mini_info` for list views, then fetch `base_info` for detailed views
- Use `titles-by-ids` endpoint for batch fetching specific titles
- Filter by `titleType` early to reduce unnecessary data processing
- Implement client-side caching for repeated queries

### TypeScript Integration
When using TypeScript, define interfaces based on the response models:
```typescript
interface Title {
  id: string;
  titleText: { text: string };
  primaryImage?: { url: string };
  titleType: { id: string; text: string };
  releaseYear?: { year: number };
  // Add other properties as needed
}

interface APIResponse<T> {
  results: T[];
  page?: number;
  next?: string;
  entries?: number;
}
```