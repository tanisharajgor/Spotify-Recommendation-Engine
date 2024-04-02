// Cypher Queries for Graph Generation & Song Recommendations

// Helper to delete graph if necessary.
match (n) detach delete n

// Graph model creation statements. These outline the attributes to include for each of the songs (nodes),
// and non-trivial similarities (edges).
load csv with headers from 'file:///Users/tanisha/Documents/hw5/nodes.csv' as node_line
create (:Song {
    id: node_line.id,
    track_id: node_line.track_id, 
    artists: node_line.artists, 
    album_name: node_line.album_name, 
    track_name: node_line.track_name, 
    popularity: node_line.popularity, 
    duration_ms: node_line.duration_ms,
    explicit: node_line.explicit,
    danceability: node_line.danceability,
    energy: node_line.energy,
    key: node_line.key,
    loudness: node_line.loudness,
    mode: node_line.mode,
    speechiness: node_line.speechiness,
    acousticness: node_line.acousticness,
    instrumentalness: node_line.instrumentalness,
    liveness: node_line.liveness,
    valence: node_line.valence,
    tempo: node_line.tempo,
    time_signature: node_line.time_signature,
    track_genre: node_line.track_genre
});

// For each of the edges, the similarity score is used as a weight to order recommendations.
load csv with headers from 'file:///Users/tanisha/Documents/hw5/edges.csv' as edge_line
merge (x:Song {id: edge_line.song_1})
merge (y:Song {id: edge_line.song_2})
merge (x)-[r:Similar]->(y)
set r.weight = edge_line.similarity;


// Recommend songs similar to those in the "Is This It" album that are not by "The Strokes".
// One can specify as many albums the query should take into consideration, when finding similar 
// links for the recommendations. They can also specify by particular songs or artist names.
// When viewed in a tabular form, these recommendations are shown in the order of strongest
// similarity score (the 1st recommendation will be the top one, etc.).
MATCH (s: Song)
WHERE s.album_name = 'Is This It' // Add further specifications here if desired.
WITH s
MATCH (s)-[r: Similar]->(similar_song: Song)
WHERE similar_song.artists <> 'The Strokes' // Or whatever artist in question (in a general sense).
RETURN similar_song
ORDER BY r.weight DESC // Orders by the similarity score, which is the weight of each edge.
LIMIT 5; // Limit to 5 recommendations.



