# Routing Table
> **Warning:** Make sure to read and understand how to [Create Luos modules](/pages/low/modules/create-modules.md) before reading this page.

The routing table is a feature of Luos allowing every <span class="cust_tooltip">[modules](/pages/overview/general-basics.md#module)<span class="cust_tooltiptext">{{ module_def }}</span></span> to own a "map" (or topology) of the entire network of your device. This map allows modules to know their physical position and to search and interact with other modules easily.<br/>
This feature is particularly used by apps modules to find other modules they need to interact with.

## Modes
As explained in [this page](/pages/overview/general-basics.md#what-is-a-node), <span class="cust_tooltip">nodes<span class="cust_tooltiptext">{{ node_def }}</span></span> can host multiple modules. To get the topology of your device, the routing table references physical connexions between your nodes and lists all the modules in each one of them.

The routing table is a table of a `routing_table_t` structure containing nodes or modules information.
The maximum number of modules and nodes are managed by the precompilation constant `MAX_MODULES_NUMBER` (set to 40 by default).

```c
route_table_t route_table[MAX_MODULES_NUMBER];
```

The routing table structure has two modes: *module entry mode* and *node entry mode*.

```c
typedef struct __attribute__((__packed__)){
    entry_mode_t mode;
    union {
        struct __attribute__((__packed__)){ // MODULE entry mode
            unsigned short id; // Module ID
            unsigned char type; /*!< Module type. */
            char alias[MAX_ALIAS_SIZE]; /*!< Module alias. */
        };
        struct __attribute__((__packed__)){ // NODE entry mode
            luos_uuid_t uuid; // Node UUID
            unsigned short port_table[4]; // Node link table
        };
    };
}routing_table_t;
```

### Module entry mode
This mode allows `route_table` to contain:
 - id: module's unique id
 - type: module's type
 - alias: module's alias

For more information, please refer to the [Modules](/pages/low/modules.md) page of this documentation.

### Node entry mode
This mode gives physical information of your devices.

The **uuid** is the serial number of the microcontroler hosting Luos. This number is unique, you can use it to identify each one of your nodes.

The **port_table** allows to share topological information of your network. Each element of this table corresponds to a physical Luos port of the node and indicates which node is connected to it by sharing a module's `id`.

Here is an example:

<img src="{{img_path}}/routing-table.png" title="Routing table">

As shown on this image, elements of the `port_table` indicate the first or last module id of the connected node through a given port.

Specific values taken by `port_table`:

 - **0**: this port is waiting to discover who is connected with. You should never see this value.
 - **0x0FFF**: this port is not connected to any other Node.

> **Note:** Routing tables can be easily displayed using [Pyluos](/pages/high/pyluos.md) through a [USB gate](/pages/high/modules_list/gate.md). Please refer to the [Pyluos routing table section](/pages/high/pyluos.md#routing-table-display) for more information.

## Search tools
The routing table library provides the following search tools to find modules and nodes' information into a Luos network:

| Description | Function | Return |
| :---: | :---: | :---: |
| Find a module's id from its alias | `id_from_alias(char* alias);` | `int` |
| Find a module's id from its type (return the first of the list) | `id_from_type(module_type_t type);` | `int` |
| Find a module's string from its type (return the first of the list) | `string_from_type(module_type_t type);` | `char*` |
| Find a module's alias from its id (return the first of the list) | `alias_from_id(uint16_t id);` | `char*` |
| Find a module's type from its id | `type_from_id(uint16_t id);` | `module_type_t` |
| Find a module's type from its alias | `type_from_alias(char* alias);` | `module_type_t` |
| Test if a module's type is a sensor | `is_sensor(module_type_t type);` | `uint8_t` |
| Get a node's id | `get_node_id(unsigned short index);` | `int` |
| Get the number of nodes in a Luos network | `get_node_nb(void);` | `int` |
| Get the list of all the nodes in a Luos network | `get_node_list(unsigned short* list);` | `void` |

## Management tools
Here are the management tools provided by the routing table library:

| Description | Function | Return |
| :---: | :---: | :---: |
| Compute the rooting table | `compute_route_table_entry_nb(void);` | `void` |
| Detect the modules in a Luos network | `detect_modules(module_t* module);` | `void` |
| Convert a node to a routing table entry | `convert_board_to_route_table(route_table_t* entry, luos_uuid_t uuid, unsigned short* port_table, int branch_nb);` | `void` |
| Convert a module to a routing table entry | `convert_module_to_route_table(route_table_t* entry, module_t* module);` | `void` |
| Insert an entry into the routing table | `insert_on_route_table(route_table_t* entry);` | `void` |
| Remove an entry in the routing table (by id) | `remove_on_route_table(int id);` | `void` |
| Erase routing table | `flush_route_table(void);` | `void` |
| Get the routing table | `get_route_table(void);` | `route_table_t*` |
| Get the last module in a Luos network | `get_last_module(void);` | `int` |
| Get the last entry in a Luos network | `get_last_entry(void);` | `int` |

<div class="cust_edit_page"><a href="https://{{gh_path}}/pages/low/modules/routing-table.md">Edit this page</a></div>
