# freecad-perimeter-holes-macro
# Create Holes Around Polygon (FreeCAD Macro)

Automatically places **evenly spaced holes around the perimeter of a closed sketch** in FreeCAD, with:

- Equal spacing between holes **and** at the ends
- Guaranteed **inward offset** from the perimeter
- Corner-safe placement (no holes too close to vertices)
- Support for angled and irregular polygon edges
- No LinearPattern, no Sketcher point micromanagement

This macro is designed for brackets, plates, panels, and enclosures where
perimeter-aware hole placement is required.

---

## Why this exists

FreeCAD currently lacks a native way to:

- Place holes evenly around an *entire perimeter*
- Respect corner offsets automatically
- Avoid topological naming issues
- Adapt hole count when geometry changes

This macro fills that gap.

---

## What it does

For each edge of a closed sketch:

1. Computes a **corner-safe usable length**
2. Derives the number of holes from a *target spacing*
3. Distributes holes **evenly** along the edge
4. Offsets holes **inward**, not outward
5. Works with straight, angled, and mixed edges

The result is visually balanced, mechanically sane hole placement.

---

## Requirements

- FreeCAD 1.0 or newer (tested on 1.1 RC)
- A **M closed sketch lying in the **XY plane**
- Python macros enabled (default)

---

## How to use

1. Open your FreeCAD document
2. Ensure your sketch:
   - Is closed
   - Represents the outer perimeter
3. Run the macro
4. Enter:
   - Sketch internal name
   - Offset from edge (mm)
   - Hole diameter (mm)
   - Minimum holes per edge
   - Target spacing (mm)
5. The macro generates hole proxies as cylinders

> Tip: These cylinders can later be converted into real PartDesign holes.

---

## Notes & limitations

- Assumes the sketch lies in the XY plane
- Offset direction is determined automatically using the sketch centroid
- Spacing is treated as a **target**, not a fixed pitch
- The macro creates geometry; it does not yet modify existing bodies

---

## License

MIT License — free to use, modify, and redistribute.

---

## Author

Created by: **Bradley Corner**  
Shared for the FreeCAD community.

Contributions, feedback, and improvements are welcome.
