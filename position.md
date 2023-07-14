## Entity Layout: POSITION 

### Synopsis 

The POSITION declaration provides a mechanism for positioning entities in the project view panel.

### Syntax 

```
point_2d ::= ( x , y )

point_3d ::= ( x , y , z )

entity_position ::= object_id : POSITION point_2d [ infoX ] [ infoY ] [ layout_order ]
  | object_id : POSITION point_3d [ infoX ] [ infoY ] [ layout_order ]
```

 -  **object_id**  *str* - The name of the entity to be positioned. This can be the type name of an entity defined in the entity library, in which case the same coordinates will be given to all the entities of that type. It can also be the instance name of the required entity. The instance name should be the complete hierarchical name.  When specifying a level name from the hierarchy, the instance name, not the type name, should be used. Each level in the hierarchy should be separated by a '.',  *i.e.* the syntax for a complete hierarchical name is: 

**top\_level\_instance\_name.next\_level\_name. ... .last\_level\_name}**. 

The position given to an instance overrides the position information of the library component.

 -  **x**  *int* - The x coordinate of the top left corner of the entity's icon. This coordinate is relative to the top left corner of the project view panel for top level entities. For child entities of a compound entity it is relative to the upper left corner of the parent entity's icon. 

The x coordinate may also be a string. In that case the string has to be the name of one of the parameters specified for the related entity. Then the position of the icon will change when the value of the parameter is changed.

 -  **y**  *int* - The y coordinate of the top left corner of the entity's icon. This coordinate is relative to the top left corner of the project view panel for top level entities. For child entities of a compound entity it is relative to the upper left corner of the parent entity's icon. 

The y coordinate may also be a string. In that case the string has to be the name of one of the parameters specified for the related entity. Then the position of the icon will change when the value of the parameter is changed.

 -  **z**  *int* - The z depth of the entity's icon. This optional parameter maybe used to modify the render order of entities. If unspecified, the z coordinate will default to '0' (considered to be the front of the screen). A high value z will push the entity deeper into the screen. For child entities the z coordinate is relative to the parent entity.
 

 -  **infoX, infoY, layout_order**  *str*
Some entities ( *e.g.* meshes) have up additional items of layout information.

### Example 

<tt> Memory\_Hierarchy.secondary\_cache : POSITION (100,50)</tt>

