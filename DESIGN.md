# Where

Tooling for group self-awareness

## Context

Groups, especially remote colaborative groups, often lack contextual information about collaborators that makes working together harder.  Co-locating oneself across a number of spaces in the context of a group (or groups) provides an important avenue for improving both sense-making and working together.  **Where** provides a generalized pattern for creating shared maps for groups to see the emergent "whereness" of each other across self-evolved sets of maps.

## Entry Types

### Space

Specifies a space, which includes a coordinate system to use for that space.  To begin the app would likely just use Cartesian coordinate system wich refers to positions on a literal image.

``` rust
struct Space {
  name: String,
  dimensionality: CoordinateSystem,
  meta: HashMap<String, String>,  // usable by the UI for whatever
}
```

#### Examples:

``` javascript
{
  name: "mountain map",
  dimensionality: {
    type: "orthogonal",
    coords: {x: "integer", y: "integer"},
    range: {x: {min: 0, max: 1024}, y:{min: 0, max: 1024}}
  },
  meta: {
      url: "https://mountain-map-images.com/everest"
  }
}
```

``` javascript
{
  name: "geo-location",
  dimensionality: {
    type: "orthogonal",
    coords: {lat: "float", lon: "float"},
    range: {lat: {min: -90, max: 90}, y:{min: -180, max: 180}}
  }
}
```

``` javascript
{
  name: "emo-location",
  dimensionality: {
    type: "orthogonal",
    coords: {core: "enum", intensity: "integer", nuance: "string"},
    range: {core: ["sad", "angry", "happy", "disgusted", "fearful", "suprised", "bad"], intesity: {min: 1, max: 5}}
  },
}
```

``` javascript
{
  name: "emo-wheel",
  dimensionality: {
    type: "fixed-tree",
    tree: {
      {
      	sad: {
          lonely: ["isolated", "abandoned"],
          vulnerable: ["victimized", "fragile"],
          despair: ["grief", "powerless"],
          ...
        },
        angry: {...},
        happy: {...},
        disgusted: {...},
        fearful: {...},
        surprised: {...},
        bad: {...}
      }
    }
  }
}
```

(See the [Feelings Wheel](https://feelingswheel.com/feelings-wheel.jpg))

### Where

Specifies a contextualized a location in a Space. Note that the meta would not need to include data already included in Entry headers like timestamp and provenance.  *Where* instances are added as links to Space entries for lookup.

``` rust
struct Where {
  location: Coord, // a location in a some arbitrary space (JSON-encoded)
  meta: HashMap<String, String>, // contextualized meaning of the location
}
```

#### Examples:

Here are some example "Wheres" in various different coordinate systems

##### Cartesian:

``` javascript
{
  location: {x: 12354, y: 725},
  meta: {rating: "best"}
}
```

##### Emo-location:

``` javascript
{
  location: {core: "angry", intesity: 5, nuance: "Feeling betrayed :("},
  meta: {current: true, quality: "worst", rank: "primary"}
}
```

##### Emo-wheel:

``` javascript
{
  location: "fragile",
  meta: {current: true, rank: "secondary"}
}
```

##### Geo-location:

``` javascript
{
  location: {lat: 32.30642, lon: -122.61458},
  meta: {type: "home", current: true}
}
```

## Zome-calls

- `create_space(Space) -> Hash`: creates a space
- `add_where(Space, Where) -> Hash`: adds a location to space
- `remove_where(Where)`: removes a location from a space
- `get_spaces() -> Vec<Space>`: returns a list of all spaces
- `get_where(Space) -> Vec<(AgentPubKey,Timestamp,Where)>`: returns list of all locations in a space

## Signals

- `added(SpaceHash, AgentPubKey, Timestamp, Where, WhereHash)`
- `removed(WhereHash)`
