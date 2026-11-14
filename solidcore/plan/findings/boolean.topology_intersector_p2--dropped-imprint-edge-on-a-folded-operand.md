# boolean.topology_intersector_p2: an imprint that drops an intersection edge (2026-11-14)

SolidCraft's Combine Cut of a lofted cavity (105 faces) by a nose piece (28 faces) deleted the
cavity; on a smaller copy of the cavity it left the cavity unchanged, and Intersect called the
overlap empty. The operands as SolidCraft passed them are
`crates/solidcore-ops/tests/data/issue_15700_cut_across_folded_cavity.bodies.json.gz`
(`target`, `tool`), and `a_boolean_whose_imprint_drops_an_intersection_edge_is_refused`
(`crates/solidcore-ops/tests/boolean.rs`) runs them.

![The plane of the tool's top face, near the cavity's nose. The section by it of the cavity's loft strip 2 runs from the tool's edge at A in to B, strip 3 runs back from B to C, strip 4 in again from C to D, strip 5 on from D. The piece of strip 4 from C to D crosses strip 2 at X and is drawn dashed: it was not imprinted. C and D are the ends of the gap.](boolean.topology_intersector_p2--dropped-imprint-edge-on-a-folded-operand.svg)

*Look at the dashed piece from C to D: it crosses strip 2 at X, where no vertex of the cavity
can be, and is missing from the imprinted bodies.*

- **The operand folds.** The cavity's surface crosses itself at its nose: its strips 2 and 4
  (record faces 2 and 4, B-spline patches over their whole domains) both pass through
  X = (3.960366, 16.913740, −1.029240), within 6e-8 and 1e-6, at v = 7.989 and 9.482, inside
  both (de Boor on the records' nets, which are the surfaces). Strip 3 lies folded between
  them. `CheckLevel::All` finds the body valid: no check tests faces of one body against each
  other. The other side of the nose (y < 0) is the mirror image.
- **The crossing has no vertex.** The tool's flat top meets strips 2 to 5 in a zig-zag, and the
  curves on strips 2 and 4 cross at X. `intersect_topology` splits both there into one proto
  vertex, which can become a vertex of only one of the two faces. `imprint_edge_definition`
  then fails for the two pieces of the strip-4 curve ("the proto vertex's final vertex is
  missing") before it edits anything.
- **The imprint went on.** `imprint_body` drops an edge definition whose imprint failed, as
  upstream does, and `intersect_insert_relate` went on past a failure that edited nothing. The
  intersection had a gap of 0.256 mm on each side of the nose: four common vertices with one
  common edge each (`mark_common`). The flood of `classify_edge_faces` put all 129 faces of
  the imprinted cavity in one set and all 46 of the tool in another. The cut kept none of the
  cavity, the intersection all of it; on the cavity cut at x < 20 (95 faces) the cut kept it
  whole and the intersection was empty. The two results were consistent with each other and
  wrong for both.
- **Where it happens elsewhere.** Measured with markers on main (all fixes on): a dropped edge
  definition occurs in none of solidcore-ops' tests and none of the rig's 1096 cases, and 34
  times in the goldens: `offset.offset_executive_p2` 20, `primitive.primitive_creation_p2` 7,
  `boolean.topology_intersector_p2` 3, `boolean.api_brep` 2, `topology.assert_valid` 2. In the
  offset, intersector and validity units no union, intersection or difference classifies
  after it; in `boolean.api_brep` and `primitive.primitive_creation_p2` at most one case each
  leaves an intersection curve ending inside a face after it. In solidcore's own tests it
  occurs 7 times in three: a fuzz case, `default_fixes_refuse_the_open_cavity_and_retain_the_input`
  and `sloped_basin_rim_chamfers_fill_the_mitre_gap`, the last with a curve ending inside a
  face after it.
- **Ways to refuse.** (A) `intersect_insert_relate` refuses whenever an edge definition was
  dropped (a flag on `ImprintOutcome`; a failing `GlueCoincidentBrepVertices` still goes on):
  it moves the goldens of the five units above. (B) A union, intersection or difference
  refuses before classifying when an edge definition was dropped and an intersection curve
  ends inside a face (a common vertex with one common edge that has no other edge in one
  body): at most the two goldens cases above and the chamfer test may move; B is not built
  yet.
- **Not at the open loop alone.** Refusing whenever an intersection curve ends inside a face
  refuses valid cases: such ends are normal at tangent contacts and touch points (a large
  cylinder resting on a box edge, two cylinders touching along a line, a union filling a void,
  `ladder/02-booleans/006-tangent-along-line-no-overlap` and its two near-tangent kin, three
  sweeps, `booleans/1649` and `booleans/2538`): 8 rig cases and 3 ops tests, none of them with a
  dropped edge.
