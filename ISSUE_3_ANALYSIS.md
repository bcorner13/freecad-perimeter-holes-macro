# Issue #3 Analysis: Sketch Validation Problems

## Issue Description
The macro previously appeared to generate holes from sketches but now creates separate solids or fails when the sketch contains points instead of closed geometry.

## Problems Identified in CreateHolesAroundPolygon.FCMacro

### 1. **No Input Validation** (Lines 8-22)
**Problem**: The function immediately accesses `sketch.Shape.Vertexes` and `sketch.Shape.Edges` without checking:
- If the sketch is valid
- If the sketch has any geometry
- If the sketch is a closed polygon
- If the sketch is a point-only sketch (PartDesign Hole type)

```python
verts = [v.Point for v in sketch.Shape.Vertexes]
```

**Impact**: 
- Point-only sketches cause no edges to be found, resulting in silent failure with generic warning
- No clear error message about incompatible sketch type

### 2. **Point-Only Sketch Not Supported** (Lines 27-93)
**Problem**: The macro relies on `sketch.Shape.Edges` to generate holes. Point-only sketches (used for PartDesign Holes) have no edges:
- `sketch.Shape.Vertexes` exists (the hole points)
- `sketch.Shape.Edges` is empty (no geometry)

**Impact**: The for loop at line 27 (`for edge in sketch.Shape.Edges:`) never executes, and the macro silently fails with "No holes were created."

### 3. **Unclear Error Messaging** (Line 88-89)
**Problem**: Generic warning doesn't explain WHY no holes were created:
```python
if not holes:
    QtGui.QMessageBox.warning(None, "Warning", "No holes were created.")
```

**Impact**: Users don't understand if:
- The sketch is incompatible (point-only)
- The offset/spacing values are too large
- The sketch has a different issue

### 4. **Missing Polygon Validation** (Line 19)
**Problem**: No check that vertexes form a valid closed polygon:
- Doesn't verify minimum 3 vertices
- Doesn't verify vertices form edges
- Doesn't handle sketches with isolated vertices

**Impact**: Malformed sketches cause confusing errors or produce unexpected results

### 5. **No Sketch Type Documentation** (Lines 8-93)
**Problem**: Function doesn't document that it requires:
- Closed polygon geometry (NOT point-only sketches)
- At least 3 vertices forming edges
- 2D sketch in XY plane

**Impact**: Users don't know why their sketch fails

## Fixes Needed

1. **Add sketch validation function** to check:
   - Sketch is not None
   - Sketch has vertexes
   - Sketch has edges
   - Sketch has at least 3 edges for a valid polygon
   - Clear error messages for each failure case

2. **Reject point-only sketches gracefully** with clear message

3. **Improve error messages** to explain what went wrong

4. **Add documentation** about required sketch type

5. **Consider alternative approach** for PartDesign Bodies (if needed)

## Root Cause (from issue #3)
Changes in FreeCAD's sketch representation exposed the macro's lack of robustness. The macro assumes all sketches have closed polygon geometry but doesn't validate this assumption.
