# Autodesk Fusion adapter

Discover the connected server's actual schema. Confirm connection, active document, design type, units, save state, and relevant capabilities before planning.

Map available tools to named parameters, sketches, features, bodies/components, joints, mass properties, interference/clearance, screenshots, and STEP/Parasolid/STL exports. Prefer named, parametric features and stable references. Use screenshots only as supplementary evidence.

Fusion APIs generally require operations to be marshalled through the add-in/application thread; use the MCP server's supported queue or bridge. Do not bypass it. If assemblies, simulation, stable IDs, transactions, or arbitrary scripts are absent, report the gap and use a neutral/manual handoff. Verify saved revision and exported geometry after mutation.
