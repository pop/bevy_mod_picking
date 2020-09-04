# 3D Mouse Picking for Bevy

This is a 3D mouse picking plugin for [Bevy](https://github.com/bevyengine/bevy). The plugin will cast a ray into the scene and check for intersection against all meshes tagged with the `PickableMesh` component. The built-in highlighting and selection state management features are opt-in.

**Super duper WIP - Issues Welcome**

![Picking demo](docs/demo.webp)

## Getting Started

To run the 3d_scene example - a modified version of the `bevy` example of the same name - clone this repository and run:
```console
cargo run --example 3d_scene
```

## Usage

### Setup

Add the repo to your dependencies in Cargo.toml

```toml
bevy_mod_picking = { git = "https://github.com/aevyrie/bevy_mod_picking", branch = "master" }
```

Import the plugin:

```rust
use bevy_mod_picking::*;
```

Add it to your App::build() in the plugins section:

```rust
.add_plugin(PickingPlugin)
```

### Marking Entities for Picking

Make sure you have your camera's entity on hand, you could do the following in your setup system:

```rust
let camera_entity = Entity::new();

// ...

.spawn_as_entity(camera_entity, Camera3dComponents {

// ...
```

Now all you have to do is mark any mesh entities with the `PickableMesh` component:

```rust
.with(PickableMesh::new(camera_entity))
```

If you want it to highlight when you hover, add the `HighlightablePickMesh` component:

```rust
.with(HighlightablePickMesh::new())
```

If you also want to select meshes and keep them highlighted with the left mouse button, add the `SelectablePickMesh` component:

```rust
.with(SelectablePickMesh::new())
```

### Getting Pick Data

Mesh picking intersection is reported in NDC. You can use the `PickState` resource to either get the topmost entity, or a list of all entities sorted by distance (near -> far) under the cursor:

```rust
fn get_picks(
    pick_state: Res<PickState>,
) {
    println!("All entities:\n{:?}", pick_state.list());
    println!("Top entity:\n{:?}", pick_state.top());
}
```

Alternatively, you can create a query to iterate over all `PickableMesh`s and get the entity's pick coordinates with `get_pick_coord_ndc()`.
