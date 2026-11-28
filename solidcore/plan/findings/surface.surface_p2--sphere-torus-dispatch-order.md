# surface.surface_p2: sphere and torus pairs took their closed form in one order only (2026-11-28)

Units: `surface.surface_p2` (`Surface::global_surface_intersect_into`, `torus_special_cases_into`),
`surface.sphere` (`Sphere::global_surface_intersect_with_into`).

![Each pair goes to the dispatch of its first surface. Before: a sphere first sent a cylinder, and a cylinder, cone or sphere first sent a torus, to the general intersector, which traces a curve, while the other order took the exact circles. After: both orders reach Sphere::intersect_with_cone or the torus's cases.](surface.surface_p2--sphere-torus-dispatch-order.svg)

*Look at the first and fourth rows: the order of the two surfaces chose between a traced curve
and the exact circles.*

**Cause.** The surface/surface dispatch picks the special cases by the first surface's kind. As
upstream, the sphere's and the torus's dispatch matched only `SmCone_TYPE`, and an `SmCylinder`
is not one; a sphere, cone, cylinder or surface of revolution first had no torus case. The
cylinder's own dispatch takes the sphere (`Sphere::intersect_with_cone`), and the torus's takes
a cone, a sphere and a surface of revolution (`intersect_revolved_into`).

**Inputs and values.** The goldens `ssi-sphere-cylinder-coaxial` (sphere radius 2 at the origin,
cylinder radius 1 about the z axis: circles at z = ±√3) and `ssi-sphere-cylinder-coaxial-tangent`
(the cylinder of radius 2, touching the sphere along its equator) gave a traced record (`[28082.533446554186, 28107.85390377505, 378923.0852227225]` and
`[14048.54210771009, 14056.542066030806, 28161.23985750358]`), their `-swapped` twins with the
cylinder first the closed form's (`[28048.0, 28073.32050807568, 378834.18584287044]` and
`[14025.0, 14033.0, 28059.0]`). Now both orders give the closed form's values bit for bit.
The test `order_dependence_tests` in `surface/surface/p2.rs` checks coaxial sphere/cylinder and
torus/cylinder, cone, sphere and revolved-line pairs in both orders against circles solved by
hand.

**Left as it is.** A surface of revolution first (`surf_of_revolution/p2.rs`) and an extrusion
first (`surf_of_extrusion/p2.rs`) still match only `SmCone_TYPE`, so a cylinder second goes to
the general intersector there.
