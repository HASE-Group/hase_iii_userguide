## ENTITYLIB - Entity Library

### Synopsis

The **ENTITYLIB** section of the EDL description enables the user to create a library of entities, the basic components from which an architecture can be created. This section is similar to the parameter library in that it creates definitions of entities which are used later in the [Structure](<https://github.com/HASE-Group/Documents/blob/main/structure.md>) section to create the architecture. Four kinds of entity can be described in the entity library, the first is a standard component, the second is a higher level component which is composed of entities (already defined in the entity library) and their interconnections. The third are meshes, which are higher level entities composed of predefined regular structures of entities and connections. The fourth is an abstract entity which inherits its behaviour from other basic entities.

### Components

- **entity\_definition:** [entity](<entity.md>) -  This is a definition of a library entity, it includes the entity's  name, description, parameters and ports.

- **higher\_entity\_definition:** [comp\_entity](<compentity.md>) - This is a definition of a higher level entity. It is almost the same as an entity definition except that it includes details of the entities that it is composed of and their interconnections.

- **mesh\_definition** - This definition is a macro allowing the creation of a list ([Mesh1D](<mesh1.md>)), an array (Mesh2D), a cuboid (Mesh3D), etc, of entities in an 
[n-dimensional mesh](<meshn.md>).

- **abstract\_definition** - This is a definition of an [abstract](meshn.md>) entity, the behaviour of which is to be inherited from other basic [entities](<entities.md>). This construct increases the reusability of entities.

[<- Defining a Project](<project.md>)