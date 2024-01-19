```Rust
fn check_for_collisions(
    mut commands: Commands,
    mut scoreboard: ResMut<Scoreboard>,
    mut paddle_query: Query<&Transform, With<Paddle>>,
    collider_query: Query<(Entity, &Transform, Option<&Brick>), With<Collider>>,
    mut collision_events: EventWriter<CollisionEvent>,
) {
    let paddle_transform = paddle_query.single_mut();
    let paddle_size = paddle_transform.scale.truncate();

    // check collision with walls
    for (collider_entity, transform, maybe_brick) in &collider_query {
        let collision = collide(
            paddle_transform.translation,
            paddle_size,
            transform.translation,
            transform.scale.truncate(),
        );
        if let Some(collision) = collision {
            // Sends a collision event so that other systems can react to the collision
            collision_events.send_default();

            // Bricks should be despawned and increment the scoreboard on collision
            if maybe_brick.is_some() {
                scoreboard.score += 1;
                commands.entity(collider_entity).despawn();
            }
        }
    }
}
```
