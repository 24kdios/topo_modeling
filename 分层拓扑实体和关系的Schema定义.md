# 分层拓扑图Schema定义

## 图Schema概述

本图Schema定义分层拓扑本体的实体(Vertex)和关系(Edge),共6层,遵循IETF RFC 8345网络拓扑标准。

---

## 一、实体(Vertex)定义

### 1.1 第1层:网络层(NetworkLayer)

#### 1.1.1 Device(设备)
```cypher
CREATE (n:Node {
  entity_id: UUID,
  entity_name: String,
  layer_number: 1,
  
  device_id: String,
  ne_ip: String,
  node_type: Enum(router,switch,optical-device,server), // 按需扩展
  vendor: String(huawei,cisco,juniper,nokia), // 按需扩展
  model: String,
  location: String,
  latitude: Float,
  longitude: Float,
  
  admin_status: Enum(up,down,maintenance),
  oper_status: Enum(up,down,degraded,unknown)
})
```

#### 1.1.2 Site(站点)
```cypher
CREATE (s:Site {
  entity_id: UUID,
  entity_name: String,
  layer_number: 1,
  
  site_id: String,
  site_name: String,
  site_type: Enum(data-center,central-office,remote-site),
  region: String,
  city: String,
  address: String,
  latitude: Float,
  longitude: Float,
  
  admin_status: Enum(up,down,maintenance),
  oper_status: Enum(up,down,degraded,unknown)
})
```

#### 1.1.3 Rack(机架)
```cypher
CREATE (r:Rack {
  entity_id: UUID,
  entity_name: String,
  layer_number: 1,
  
  rack_id: String,
  rack_number: String,
  rack_type: Enum(standard,custom),
  height_u: Integer,
  vendor: String,
  location: String
})
```

#### 1.1.4 Shelf(子架)
```cypher
CREATE (sh:Shelf {
  entity_id: UUID,
  entity_name: String,
  layer_number: 1,
  
  shelf_id: String,
  shelf_number: String,
  shelf_type: Enum,
  vendor: String,
  model: String
})
```

#### 1.1.5 Board(板卡)
```cypher
CREATE (b:Board {
  entity_id: UUID,
  entity_name: String,
  layer_number: 1,
  
  board_id: String,
  board_number: String,
  board_type: Enum(line-card,control-card,service-card),
  slot_number: String,
  vendor: String,
  model: String,
  serial_number: String
})
```

#### 1.1.6 PhysicalPort(物理端口)
```cypher
CREATE (pp:PhysicalPort {
  entity_id: UUID,
  entity_name: String,
  layer_number: 1,
  
  port_id: String,
  port_name: String,
  port_number: String,
  port_type: Enum(ethernet,optical,serial),
  speed: String,
  duplex: Enum(full,half,auto),
  mtu: Integer,
  mac_address: String
})
```

#### 1.1.7 OpticalModule(光模块)
```cypher
CREATE (om:OpticalModule {
  entity_id: UUID,
  entity_name: String,
  layer_number: 1,
  
  module_id: String,
  module_name: String,
  module_type: Enum(sfp,sfp+,qsfp,qsfp28),
  wavelength: String,
  transmit_power: Float,
  receive_power: Float,
  vendor: String,
  serial_number: String
})
```

#### 1.1.8 OpticalFiber(光纤)
```cypher
CREATE (of:OpticalFiber {
  entity_id: UUID,
  entity_name: String,
  layer_number: 1,
  
  fiber_id: String,
  fiber_name: String,
  fiber_type: Enum(single-mode,multi-mode),
  fiber_length: Float,
  wavelength: String,
  connector_type: Enum(lc,sc,mPO),
  cable_id: String
})
```

#### 1.1.9 Fan(风扇)
```cypher
CREATE (f:Fan {
  entity_id: UUID,
  entity_name: String,
  layer_number: 1,
  
  fan_id: String,
  fan_number: String,
  fan_type: Enum,
  speed: Integer,
  vendor: String,
  model: String
})
```

---

### 1.2 第2层:链路层(LinkLayer)

#### 1.2.1 Link(链路)
```cypher
CREATE (l:Link {
  entity_id: UUID,
  entity_name: String,
  layer_number: 2,
  
  link_id: String,
  link_name: String,
  link_type: Enum(point-to-point,multi-point,broadcast),
  link_layer: Enum(layer2,layer3),
  bandwidth: Float,
  delay: Float,
  loss_rate: Float,
  source_device: String,
  destination_device: String,
  source_tp: String,
  destination_tp: String,
  
  admin_status: Enum(up,down,maintenance),
  oper_status: Enum(up,down,degraded,unknown),
  
  creation_time: DateTime,
  last_update_time: DateTime,
  data_source: String
})
```

#### 1.2.2 LAG(链路聚合组)
```cypher
CREATE (lag:LAG {
  entity_id: UUID,
  entity_name: String,
  layer_number: 2,
  
  lag_id: String,
  lag_name: String,
  lag_type: Enum(static,lacp),
  member_links: List<String>,
  lag_mode: Enum(active,passive),
  bandwidth: Float,
  load_balance_mode: Enum,
  
  admin_status: Enum(up,down,maintenance),
  oper_status: Enum(up,down,degraded,unknown),
  
  creation_time: DateTime,
  last_update_time: DateTime,
  data_source: String
})
```

#### 1.2.3 VLAN(虚拟局域网)
```cypher
CREATE (vlan:VLAN {
  entity_id: UUID,
  entity_name: String,
  layer_number: 2,
  
  vlan_id: String,
  vlan_number: Integer,
  vlan_name: String,
  vlan_type: Enum(default,q-in-q,private),
  description: String,
  
  admin_status: Enum(up,down,maintenance),
  oper_status: Enum(up,down,degraded,unknown),
  
  creation_time: DateTime,
  last_update_time: DateTime,
  data_source: String
})
```

#### 1.2.4 TerminationPoint(终结点)
```cypher
CREATE (tp:TerminationPoint {
  entity_id: UUID,
  entity_name: String,
  layer_number: 2,
  
  tp_id: String,
  tp_name: String,
  tp_type: Enum(physical,logical,virtual),
  layer_protocol: Enum(ethernet,mpls,ip),
  node_id: String,
  port_id: String,
  
  admin_status: Enum(up,down,maintenance),
  oper_status: Enum(up,down,degraded,unknown),
  
  creation_time: DateTime,
  last_update_time: DateTime,
  data_source: String
})
```

#### 1.2.5 LogicalInterface(逻辑接口)
```cypher
CREATE (li:LogicalInterface {
  entity_id: UUID,
  entity_name: String,
  layer_number: 2,
  
  interface_id: String,
  interface_name: String,
  interface_type: Enum(layer3,virtual),
  ip_address: String,
  subnet_mask: String,
  mtu: Integer,
  mac_address: String,
  
  admin_status: Enum(up,down,maintenance),
  oper_status: Enum(up,down,degraded,unknown),
  
  creation_time: DateTime,
  last_update_time: DateTime,
  data_source: String
})
```

#### 1.2.6 SubInterface(子接口)
```cypher
CREATE (si:SubInterface {
  entity_id: UUID,
  entity_name: String,
  layer_number: 2,
  
  sub_interface_id: String,
  sub_interface_name: String,
  parent_interface: String,
  sub_interface_number: Integer,
  encapsulation: Enum(dot1q,qinq),
  vlan_id: Integer,
  ip_address: String,
  subnet_mask: String,
  
  admin_status: Enum(up,down,maintenance),
  oper_status: Enum(up,down,degraded,unknown),
  
  creation_time: DateTime,
  last_update_time: DateTime,
  data_source: String
})
```

#### 1.2.7 LoopbackInterface(环回接口)
```cypher
CREATE (lo:LoopbackInterface {
  entity_id: UUID,
  entity_name: String,
  layer_number: 2,
  
  loopback_id: String,
  loopback_name: String,
  loopback_number: Integer,
  ip_address: String,
  subnet_mask: String,
  router_id: Boolean,
  
  admin_status: Enum(up,down,maintenance),
  oper_status: Enum(up,down,degraded,unknown),
  
  creation_time: DateTime,
  last_update_time: DateTime,
  data_source: String
})
```

#### 1.2.8 VLANInterface(VLAN接口)
```cypher
CREATE (vi:VLANInterface {
  entity_id: UUID,
  entity_name: String,
  layer_number: 2,
  
  vlan_interface_id: String,
  vlan_interface_name: String,
  vlan_id: Integer,
  ip_address: String,
  subnet_mask: String,
  mac_address: String,
  
  admin_status: Enum(up,down,maintenance),
  oper_status: Enum(up,down,degraded,unknown),
  
  creation_time: DateTime,
  last_update_time: DateTime,
  data_source: String
})
```

#### 1.2.9 BridgeDomain(桥接域)
```cypher
CREATE (bd:BridgeDomain {
  entity_id: UUID,
  entity_name: String,
  layer_number: 2,
  
  bridge_domain_id: String,
  bridge_domain_name: String,
  bridge_domain_type: Enum(vlan,vxlan,pbb),
  vni: Integer,
  vlan_list: List<Integer>,
  flooding_mode: Enum(enabled,disabled),
  
  admin_status: Enum(up,down,maintenance),
  oper_status: Enum(up,down,degraded,unknown),
  
  creation_time: DateTime,
  last_update_time: DateTime,
  data_source: String
})
```

---

### 1.3 第3层:路由层(RoutingLayer)

#### 1.3.1 RoutingInstance(路由实例)
```cypher
CREATE (ri:RoutingInstance {
  entity_id: UUID,
  entity_name: String,
  layer_number: 3,
  
  routing_instance_id: String,
  routing_instance_name: String,
  routing_instance_type: Enum(vrf,global,vpn-instance),
  route_distinguisher: String,
  route_target: List<String>,
  vrf_id: Integer,
  description: String,
  
  admin_status: Enum(up,down,maintenance),
  oper_status: Enum(up,down,degraded,unknown),
  
  creation_time: DateTime,
  last_update_time: DateTime,
  data_source: String
})
```

#### 1.3.2 Route(路由条目)
```cypher
CREATE (r:Route {
  entity_id: UUID,
  entity_name: String,
  layer_number: 3,
  
  route_id: String,
  route_type: Enum(static,connected,bgp,ospf,is-is,rip),
  destination_prefix: String,
  next_hop: String,
  outgoing_interface: String,
  route_preference: Integer,
  metric: Integer,
  route_tag: String,
  
  admin_status: Enum(up,down,maintenance),
  oper_status: Enum(up,down,degraded,unknown),
  
  creation_time: DateTime,
  last_update_time: DateTime,
  data_source: String
})
```

#### 1.3.3 OSPFArea(OSPF区域)
```cypher
CREATE (oa:OSPFArea {
  entity_id: UUID,
  entity_name: String,
  layer_number: 3,
  
  ospf_area_id: String,
  area_id: String,
  area_type: Enum(normal,stub,nssa),
  router_count: Integer,
  lsa_count: Integer,
  default_cost: Integer,
  description: String,
  
  admin_status: Enum(up,down,maintenance),
  oper_status: Enum(up,down,degraded,unknown),
  
  creation_time: DateTime,
  last_update_time: DateTime,
  data_source: String
})
```

#### 1.3.4 BGPProcess(BGP进程)
```cypher
CREATE (bgp:BGPProcess {
  entity_id: UUID,
  entity_name: String,
  layer_number: 3,
  
  bgp_process_id: String,
  bgp_as_number: Integer,
  router_id: String,
  local_as: Integer,
  bgp_identifier: String,
  cluster_id: String,
  graceful_restart: Boolean,
  description: String,
  
  admin_status: Enum(up,down,maintenance),
  oper_status: Enum(up,down,degraded,unknown),
  
  creation_time: DateTime,
  last_update_time: DateTime,
  data_source: String
})
```

#### 1.3.5 IS-ISProcess(IS-IS进程)
```cypher
CREATE (isis:IS-ISProcess {
  entity_id: UUID,
  entity_name: String,
  layer_number: 3,
  
  isis_process_id: String,
  isis_instance_name: String,
  isis_level: Enum(level-1,level-2,level-1-2),
  system_id: String,
  area_address: String,
  net_address: String,
  isis_type: Enum(narrow,wide),
  description: String,
  
  admin_status: Enum(up,down,maintenance),
  oper_status: Enum(up,down,degraded,unknown),
  
  creation_time: DateTime,
  last_update_time: DateTime,
  data_source: String
})
```

#### 1.3.6 RoutePolicy(路由策略)
```cypher
CREATE (rp:RoutePolicy {
  entity_id: UUID,
  entity_name: String,
  layer_number: 3,
  
  policy_id: String,
  policy_name: String,
  policy_type: Enum(filter,route-map,prefix-list),
  match_criteria: List<String>,
  set_actions: List<String>,
  permit: Boolean,
  sequence: Integer,
  description: String,
  
  admin_status: Enum(up,down,maintenance),
  oper_status: Enum(up,down,degraded,unknown),
  
  creation_time: DateTime,
  last_update_time: DateTime,
  data_source: String
})
```

#### 1.3.7 StaticRoute(静态路由)
```cypher
CREATE (sr:StaticRoute {
  entity_id: UUID,
  entity_name: String,
  layer_number: 3,
  
  static_route_id: String,
  destination_prefix: String,
  next_hop: String,
  outgoing_interface: String,
  route_preference: Integer,
  tag: String,
  permanent: Boolean,
  description: String,
  
  admin_status: Enum(up,down,maintenance),
  oper_status: Enum(up,down,degraded,unknown),
  
  creation_time: DateTime,
  last_update_time: DateTime,
  data_source: String
})
```

---

### 1.4 第4层:隧道层(TunnelLayer)

#### 1.4.1 Tunnel(隧道实例)
```cypher
CREATE (t:Tunnel {
  entity_id: UUID,
  entity_name: String,
  layer_number: 4,
  
  tunnel_id: String,
  tunnel_name: String,
  tunnel_type: Enum(mpls-te,sr-te,gre,vxlan,ipsec),
  source_node: String,
  destination_node: String,
  source_ip: String,
  destination_ip: String,
  tunnel_number: Integer,
  bandwidth: Float,
  encoding_type: Enum(mpls,ethernet,ip),
  signaling_protocol: Enum(rsvp-te,ldp,sr-bgp,static),
  priority: Integer,
  path_setup_type: Enum(explicit,computed,dynamic),
  description: String,
  
  admin_status: Enum(up,down,maintenance),
  oper_status: Enum(up,down,degraded,unknown),
  
  creation_time: DateTime,
  last_update_time: DateTime,
  data_source: String
})
```

#### 1.4.2 TunnelTemplate(隧道模板)
```cypher
CREATE (tt:TunnelTemplate {
  entity_id: UUID,
  entity_name: String,
  layer_number: 4,
  
  template_id: String,
  template_name: String,
  template_type: Enum(mpls-te,sr-te,gre),
  default_bandwidth: Float,
  default_priority: Integer,
  default_path_constraints: Map,
  default_metric_type: Enum(igp,te,hop-count),
  hot_standby: Boolean,
  frr: Boolean,
  description: String,
  
  admin_status: Enum(up,down,maintenance),
  oper_status: Enum(up,down,degraded,unknown),
  
  creation_time: DateTime,
  last_update_time: DateTime,
  data_source: String
})
```

#### 1.4.3 TunnelPath(隧道路径)
```cypher
CREATE (tp:TunnelPath {
  entity_id: UUID,
  entity_name: String,
  layer_number: 4,
  
  path_id: String,
  path_name: String,
  path_type: Enum(primary,backup,standby),
  path_role: Enum(working,protecting),
  explicit_path: List<String>,
  computed_path: List<String>,
  metric: Float,
  bandwidth: Float,
  hop_count: Integer,
  path_setup_method: Enum(explicit,computed,dynamic),
  path_constraints: Map,
  description: String,
  
  admin_status: Enum(up,down,maintenance),
  oper_status: Enum(up,down,degraded,unknown),
  
  creation_time: DateTime,
  last_update_time: DateTime,
  data_source: String
})
```

#### 1.4.4 LSP(标签交换路径)
```cypher
CREATE (lsp:LSP {
  entity_id: UUID,
  entity_name: String,
  layer_number: 4,
  
  lsp_id: String,
  lsp_name: String,
  lsp_type: Enum(rsvp-te,ldp,static),
  source_node: String,
  destination_node: String,
  label_stack: List<Integer>,
  bandwidth: Float,
  lsp_direction: Enum(unidirectional,bidirectional),
  lsp_role: Enum(working,protecting),
  signaling_status: Enum,
  path_state: Enum(up,down),
  description: String,
  
  admin_status: Enum(up,down,maintenance),
  oper_status: Enum(up,down,degraded,unknown),
  
  creation_time: DateTime,
  last_update_time: DateTime,
  data_source: String
})
```

#### 1.4.5 SRPolicy(Segment Routing策略)
```cypher
CREATE (srp:SRPolicy {
  entity_id: UUID,
  entity_name: String,
  layer_number: 4,
  
  sr_policy_id: String,
  sr_policy_name: String,
  policy_color: Integer,
  endpoint: String,
  headend: String,
  binding_sid: Integer,
  candidate_paths: List<String>,
  segment_list: List<String>,
  policy_type: Enum(explicit,computed,dynamic),
  preference: Integer,
  bandwidth: Float,
  description: String,
  
  admin_status: Enum(up,down,maintenance),
  oper_status: Enum(up,down,degraded,unknown),
  
  creation_time: DateTime,
  last_update_time: DateTime,
  data_source: String
})
```

#### 1.4.6 TunnelInterface(隧道接口)
```cypher
CREATE (ti:TunnelInterface {
  entity_id: UUID,
  entity_name: String,
  layer_number: 4,
  
  tunnel_interface_id: String,
  tunnel_interface_name: String,
  interface_number: Integer,
  tunnel_id: String,
  source_ip: String,
  destination_ip: String,
  mtu: Integer,
  tunnel_mode: Enum(mpls,gre,vxlan,ipsec),
  encapsulation: Enum,
  description: String,
  
  admin_status: Enum(up,down,maintenance),
  oper_status: Enum(up,down,degraded,unknown),
  
  creation_time: DateTime,
  last_update_time: DateTime,
  data_source: String
})
```

#### 1.4.7 TunnelProtection(隧道保护)
```cypher
CREATE (tp:TunnelProtection {
  entity_id: UUID,
  entity_name: String,
  layer_number: 4,
  
  protection_id: String,
  protection_type: Enum(hot-standby,frr,path-protection),
  backup_path_id: String,
  switchover_mode: Enum(manual,automatic),
  revertive_mode: Enum(revertive,non-revertive),
  hold_time: Integer,
  wait_to_revert: Integer,
  protection_group_id: String,
  description: String,
  
  admin_status: Enum(up,down,maintenance),
  oper_status: Enum(up,down,degraded,unknown),
  protection_status: Enum(working,protecting),
  
  creation_time: DateTime,
  last_update_time: DateTime,
  data_source: String
})
```

#### 1.4.8 TEClass(流量工程类)
```cypher
CREATE (te:TEClass {
  entity_id: UUID,
  entity_name: String,
  layer_number: 4,
  
  te_class_id: String,
  te_class_name: String,
  class_number: Integer,
  class_type: Enum(ct0,ct1,ct2,ct3),
  priority: Integer,
  bandwidth_constraint: Float,
  preemption_priority: Integer,
  description: String,
  
  admin_status: Enum(up,down,maintenance),
  oper_status: Enum(up,down,degraded,unknown),
  
  creation_time: DateTime,
  last_update_time: DateTime,
  data_source: String
})
```

#### 1.4.9 ExplicitPath(显式路径)
```cypher
CREATE (ep:ExplicitPath {
  entity_id: UUID,
  entity_name: String,
  layer_number: 4,
  
  explicit_path_id: String,
  explicit_path_name: String,
  path_hop_list: List<Map>,
  path_type: Enum(strict,loose),
  description: String,
  
  admin_status: Enum(up,down,maintenance),
  oper_status: Enum(up,down,degraded,unknown),
  
  creation_time: DateTime,
  last_update_time: DateTime,
  data_source: String
})
```

#### 1.4.10 SegmentList(Segment列表)
```cypher
CREATE (sl:SegmentList {
  entity_id: UUID,
  entity_name: String,
  layer_number: 4,
  
  segment_list_id: String,
  segment_list_name: String,
  segment_type: Enum(adjacency,node,binding-sid),
  segment_list: List<Map>,
  weight: Integer,
  description: String,
  
  admin_status: Enum(up,down,maintenance),
  oper_status: Enum(up,down,degraded,unknown),
  
  creation_time: DateTime,
  last_update_time: DateTime,
  data_source: String
})
```

#### 1.4.11 RSVPTESession(RSVP-TE会话)
```cypher
CREATE (rs:RSVPTESession {
  entity_id: UUID,
  entity_name: String,
  layer_number: 4,
  
  rsvp_session_id: String,
  session_name: String,
  session_type: Enum,
  tunnel_id: String,
  lsp_id: String,
  source_ip: String,
  destination_ip: String,
  session_state: Enum(up,down),
  rsvp_hellos: Integer,
  description: String,
  
  admin_status: Enum(up,down,maintenance),
  oper_status: Enum(up,down,degraded,unknown),
  
  creation_time: DateTime,
  last_update_time: DateTime,
  data_source: String
})
```

#### 1.4.12 PathComputation(路径计算)
```cypher
CREATE (pc:PathComputation {
  entity_id: UUID,
  entity_name: String,
  layer_number: 4,
  
  path_computation_id: String,
  computation_type: Enum(cspf,pce,local),
  computation_status: Enum(success,failure,in-progress),
  algorithm_type: Enum(igp,te,sr),
  path_constraints: Map,
  result_path: String,
  computation_node: String,
  description: String,
  
  admin_status: Enum(up,down,maintenance),
  oper_status: Enum(up,down,degraded,unknown),
  
  creation_time: DateTime,
  last_update_time: DateTime,
  data_source: String
})
```

#### 1.4.13 TunnelGroup(隧道组)
```cypher
CREATE (tg:TunnelGroup {
  entity_id: UUID,
  entity_name: String,
  layer_number: 4,
  
  tunnel_group_id: String,
  tunnel_group_name: String,
  group_type: Enum(bundle,protection,multipath),
  member_tunnels: List<String>,
  load_balance_mode: Enum,
  failover_mode: Enum,
  description: String,
  
  admin_status: Enum(up,down,maintenance),
  oper_status: Enum(up,down,degraded,unknown),
  
  creation_time: DateTime,
  last_update_time: DateTime,
  data_source: String
})
```

---

### 1.5 第5层:Service层(ServiceLayer)

#### 1.5.1 VPNInstance(VPN实例)
```cypher
CREATE (vpn:VPNInstance {
  entity_id: UUID,
  entity_name: String,
  layer_number: 5,
  
  vpn_instance_id: String,
  vpn_instance_name: String,
  vpn_type: Enum(l3vpn,l2vpn,vpls),
  vpn_id: Integer,
  route_distinguisher: String,
  route_target: List<String>,
  vrf_name: String,
  vpn_topology: Enum(hub-spoke,full-mesh),
  customer_count: Integer,
  site_count: Integer,
  description: String,
  
  admin_status: Enum(up,down,maintenance),
  oper_status: Enum(up,down,degraded,unknown),
  
  creation_time: DateTime,
  last_update_time: DateTime,
  data_source: String
})
```

#### 1.5.2 EVPNInstance(EVPN实例)
```cypher
CREATE (evpn:EVPNInstance {
  entity_id: UUID,
  entity_name: String,
  layer_number: 5,
  
  evpn_instance_id: String,
  evpn_instance_name: String,
  evpn_type: Enum(evpn-vxlan,evpn-mpls),
  evpn_id: Integer,
  evi: Integer,
  vni: Integer,
  mac_vrf: String,
  arp_suppression: Boolean,
  mac_learning_mode: Enum(control-plane,data-plane),
  mac_limit: Integer,
  description: String,
  
  admin_status: Enum(up,down,maintenance),
  oper_status: Enum(up,down,degraded,unknown),
  
  creation_time: DateTime,
  last_update_time: DateTime,
  data_source: String
})
```

#### 1.5.3 NetworkService(网络服务)
```cypher
CREATE (ns:NetworkService {
  entity_id: UUID,
  entity_name: String,
  layer_number: 5,
  
  network_service_id: String,
  network_service_name: String,
  service_type: Enum(vpn,internet,transport,mpls),
  service_tier: Enum(gold,silver,bronze),
  service_provider: String,
  service_domain: String,
  service_topology: Enum(point-to-point,hub-spoke,any-to-any),
  service_scope: Enum(intra-domain,inter-domain),
  sla_id: String,
  description: String,
  
  admin_status: Enum(up,down,maintenance),
  oper_status: Enum(up,down,degraded,unknown),
  
  creation_time: DateTime,
  last_update_time: DateTime,
  data_source: String
})
```

#### 1.5.4 ServiceInstance(服务实例)
```cypher
CREATE (si:ServiceInstance {
  entity_id: UUID,
  entity_name: String,
  layer_number: 5,
  
  service_instance_id: String,
  service_instance_name: String,
  instance_type: Enum(vpn-service,evpn-service,vpls-service),
  service_id: String,
  node_id: String,
  attachment_interface: String,
  service_vlan: Integer,
  qos_policy: String,
  bandwidth: Float,
  description: String,
  
  admin_status: Enum(up,down,maintenance),
  oper_status: Enum(up,down,degraded,unknown),
  
  creation_time: DateTime,
  last_update_time: DateTime,
  data_source: String
})
```

#### 1.5.5 VXLANVNI(VXLAN网络标识符)
```cypher
CREATE (vni:VXLANVNI {
  entity_id: UUID,
  entity_name: String,
  layer_number: 5,
  
  vni_id: String,
  vni_number: Integer,
  vni_name: String,
  vxlan_instance: String,
  vtep_ip: String,
  multicast_group: String,
  bridge_domain: String,
  evpn_instance: String,
  description: String,
  
  admin_status: Enum(up,down,maintenance),
  oper_status: Enum(up,down,degraded,unknown),
  
  creation_time: DateTime,
  last_update_time: DateTime,
  data_source: String
})
```

#### 1.5.6 ServiceAccessPoint(服务接入点)
```cypher
CREATE (sap:ServiceAccessPoint {
  entity_id: UUID,
  entity_name: String,
  layer_number: 5,
  
  sap_id: String,
  sap_name: String,
  sap_type: Enum(customer-site,provider-edge),
  service_id: String,
  node_id: String,
  interface_id: String,
  encapsulation: Enum(vlan,qinq,vxlan,mpls),
  vlan_id: Integer,
  outer_vlan: Integer,
  inner_vlan: Integer,
  sap_role: Enum(uni,nni),
  description: String,
  
  admin_status: Enum(up,down,maintenance),
  oper_status: Enum(up,down,degraded,unknown),
  
  creation_time: DateTime,
  last_update_time: DateTime,
  data_source: String
})
```

---

### 1.6 第6层:Business层(BusinessLayer)

#### 1.6.1 BusinessService(业务服务)
```cypher
CREATE (bs:BusinessService {
  entity_id: UUID,
  entity_name: String,
  layer_number: 6,
  
  business_service_id: String,
  business_service_name: String,
  service_category: Enum(mobile-enterprise,wholesale,retail),
  service_type: Enum(base-station,enterprise-line,vpn-service,transit),
  service_level: Enum(premium,standard,basic),
  service_provider: String,
  service_region: String,
  activation_date: Date,
  termination_date: Date,
  service_status: Enum(active,suspended,terminated),
  billing_model: Enum(flat-rate,usage-based,hybrid),
  description: String,
  
  admin_status: Enum(up,down,maintenance),
  oper_status: Enum(up,down,degraded,unknown),
  
  creation_time: DateTime,
  last_update_time: DateTime,
  data_source: String
})
```

#### 1.6.2 BaseStationService(基站业务)
```cypher
CREATE (bss:BaseStationService {
  entity_id: UUID,
  entity_name: String,
  layer_number: 6,
  
  base_station_service_id: String,
  base_station_name: String,
  base_station_id: String,
  base_station_type: Enum(macro-cell,small-cell,distributed-antenna),
  mobile_operator: String,
  cell_site_id: String,
  frequency_band: String,
  technology: Enum(4g,5g,nr),
  backhaul_type: Enum(fiber,microwave,ptn),
  backhaul_bandwidth: Float,
  site_location: String,
  latitude: Float,
  longitude: Float,
  service_id: String,
  description: String,
  
  admin_status: Enum(up,down,maintenance),
  oper_status: Enum(up,down,degraded,unknown),
  
  creation_time: DateTime,
  last_update_time: DateTime,
  data_source: String
})
```

#### 1.6.3 EnterpriseLeasedLine(企业专线)
```cypher
CREATE (ell:EnterpriseLeasedLine {
  entity_id: UUID,
  entity_name: String,
  layer_number: 6,
  
  leased_line_id: String,
  leased_line_name: String,
  customer_name: String,
  customer_id: String,
  line_type: Enum(mstp,otn,ip-mpls,packet-leased-line),
  line_grade: Enum(gold,silver,bronze),
  line_speed: Float,
  line_distance: Float,
  access_point_a: String,
  access_point_b: String,
  sla_id: String,
  service_id: String,
  activation_date: Date,
  termination_date: Date,
  billing_cycle: Enum(monthly,quarterly,yearly),
  description: String,
  
  admin_status: Enum(up,down,maintenance),
  oper_status: Enum(up,down,degraded,unknown),
  
  creation_time: DateTime,
  last_update_time: DateTime,
  data_source: String
})
```

---

## 二、关系(Edge)定义

### 2.1 层内关系(Intra-Layer)

#### 2.1.1 第1层:网络层内关系

**包含关系**
```cypher
CREATE (n:Node)-[:CONTAINS]->(r:Rack)
CREATE (r:Rack)-[:CONTAINS]->(sh:Shelf)
CREATE (sh:Shelf)-[:CONTAINS]->(b:Board)
CREATE (b:Board)-[:CONTAINS]->(pp:PhysicalPort)
CREATE (pp:PhysicalPort)-[:CONTAINS]->(om:OpticalModule)
CREATE (n:Node)-[:CONTAINS]->(f:Fan)
```

**反向关系**
```cypher
CREATE (r:Rack)-[:BELONGS_TO]->(n:Node)
CREATE (sh:Shelf)-[:BELONGS_TO]->(r:Rack)
CREATE (b:Board)-[:BELONGS_TO]->(sh:Shelf)
CREATE (pp:PhysicalPort)-[:BELONGS_TO]->(b:Board)
CREATE (om:OpticalModule)-[:BELONGS_TO]->(pp:PhysicalPort)
```

**连接关系**
```cypher
CREATE (pp:PhysicalPort)-[:CONNECTS_TO]->(of:OpticalFiber)
CREATE (of:OpticalFiber)-[:CONNECTED_BY]->(pp:PhysicalPort)
```

**层级关系**
```cypher
CREATE (s:Site)-[:CONTAINS]->(n:Node)
CREATE (n:Node)-[:BELONGS_TO]->(s:Site)
```

**冗余关系**
```cypher
CREATE (n1:Node)-[:PRIMARY_BACKUP]->(n2:Node)
CREATE (f1:Fan)-[:PRIMARY_BACKUP]->(f2:Fan)
```

**对等关系**
```cypher
CREATE (n1:Node)-[:STACKS_WITH]->(n2:Node)
CREATE (n2:Node)-[:STACKS_WITH]->(n1:Node)
```

**短接关系(Shortcut)**
```cypher
CREATE (n:Node)-[:HAS_PORT]->(pp:PhysicalPort)
CREATE (n:Node)-[:HAS_MODULE]->(om:OpticalModule)
CREATE (n:Node)-[:CONNECTS_FIBER]->(of:OpticalFiber)
CREATE (s:Site)-[:HAS_NODE]->(n:Node)
CREATE (s:Site)-[:HAS_PORT]->(pp:PhysicalPort)
```

---

#### 2.1.2 第2层:链路层内关系

**包含关系**
```cypher
CREATE (n:Node)-[:CONTAINS]->(li:LogicalInterface)
CREATE (n:Node)-[:CONTAINS]->(lo:LoopbackInterface)
CREATE (li:LogicalInterface)-[:PROVIDES]->(si:SubInterface)
```

**连接关系**
```cypher
CREATE (pp:PhysicalPort)-[:PROVIDES]->(tp:TerminationPoint)
CREATE (tp:TerminationPoint)-[:CONNECTS_TO]->(l:Link)
CREATE (pp:PhysicalPort)-[:CONNECTS_TO]->(l:Link)
CREATE (l:Link)-[:CONNECTED_BY]->(pp:PhysicalPort)
```

**成员关系**
```cypher
CREATE (pp:PhysicalPort)-[:MEMBER_OF]->(lag:LAG)
CREATE (lag:LAG)-[:HAS_MEMBER]->(pp:PhysicalPort)
CREATE (lag:LAG)-[:AGGREGATES]->(l:Link)
CREATE (l:Link)-[:AGGREGATED_BY]->(lag:LAG)
```

**VLAN关系**
```cypher
CREATE (pp:PhysicalPort)-[:PROVIDES]->(si:SubInterface)
CREATE (si:SubInterface)-[:BELONGS_TO]->(vlan:VLAN)
CREATE (vlan:VLAN)-[:PROVIDES]->(vi:VLANInterface)
CREATE (vlan:VLAN)-[:BELONGS_TO]->(bd:BridgeDomain)
CREATE (bd:BridgeDomain)-[:CONTAINS]->(tp:TerminationPoint)
```

**冗余关系**
```cypher
CREATE (l1:Link)-[:PRIMARY_BACKUP]->(l2:Link)
CREATE (lag1:LAG)-[:CROSS_DEVICE_LAG]->(lag2:LAG)
```

**对等关系**
```cypher
CREATE (l1:Link)-[:PARALLEL_WITH]->(l2:Link)
CREATE (bd1:BridgeDomain)-[:PEER_WITH]->(bd2:BridgeDomain)
```

**短接关系(Shortcut)**
```cypher
CREATE (n:Node)-[:CONNECTS_LINK]->(l:Link)
CREATE (n:Node)-[:HAS_VLAN]->(vlan:VLAN)
CREATE (l:Link)-[:CARRIES_VLAN]->(vlan:VLAN)
```

---

#### 2.1.3 第3层:路由层内关系

**包含关系**
```cypher
CREATE (n:Node)-[:CONTAINS]->(ri:RoutingInstance)
CREATE (ri:RoutingInstance)-[:CONTAINS]->(r:Route)
CREATE (ri:RoutingInstance)-[:CONTAINS]->(oa:OSPFArea)
CREATE (ri:RoutingInstance)-[:CONTAINS]->(sr:StaticRoute)
```

**运行关系**
```cypher
CREATE (ri:RoutingInstance)-[:RUNS]->(bgp:BGPProcess)
CREATE (ri:RoutingInstance)-[:RUNS]->(isis:IS-ISProcess)
```

**生成关系**
```cypher
CREATE (bgp:BGPProcess)-[:GENERATES]->(r:Route)
CREATE (isis:IS-ISProcess)-[:GENERATES]->(r:Route)
CREATE (oa:OSPFArea)-[:GENERATES]->(r:Route)
CREATE (r:Route)-[:GENERATED_BY]->(bgp:BGPProcess)
```

**应用关系**
```cypher
CREATE (bgp:BGPProcess)-[:APPLIES]->(rp:RoutePolicy)
CREATE (isis:IS-ISProcess)-[:APPLIES]->(rp:RoutePolicy)
CREATE (rp:RoutePolicy)-[:APPLIED_BY]->(bgp:BGPProcess)
```

**匹配关系**
```cypher
CREATE (r:Route)-[:MATCHES]->(rp:RoutePolicy)
```

**冗余关系**
```cypher
CREATE (bgp1:BGPProcess)-[:BGP_NEIGHBOR]->(bgp2:BGPProcess)
CREATE (isis1:IS-ISProcess)-[:ISIS_NEIGHBOR]->(isis2:IS-ISProcess)
CREATE (ri1:RoutingInstance)-[:PEER_WITH]->(ri2:RoutingInstance)
```

**对等关系**
```cypher
CREATE (r1:Route)-[:ECMP_WITH]->(r2:Route)
CREATE (bgp1:BGPProcess)-[:PEER_GROUP]->(bgp2:BGPProcess)
CREATE (oa1:OSPFArea)-[:CONNECTS_TO]->(oa2:OSPFArea)
```

**短接关系(Shortcut)**
```cypher
CREATE (n:Node)-[:HAS_ROUTE]->(r:Route)
CREATE (n:Node)-[:HAS_BGP]->(bgp:BGPProcess)
CREATE (n:Node)-[:HAS_ISIS]->(isis:IS-ISProcess)
CREATE (r:Route)-[:BELONGS_TO_AREA]->(oa:OSPFArea)
```

---

#### 2.1.4 第4层:隧道层内关系

**包含关系**
```cypher
CREATE (n:Node)-[:HOSTS]->(t:Tunnel)
CREATE (t:Tunnel)-[:CONTAINS]->(tp:TunnelPath)
CREATE (t:Tunnel)-[:CONTAINS]->(lsp:LSP)
CREATE (t:Tunnel)-[:CONTAINS]->(srp:SRPolicy)
CREATE (srp:SRPolicy)-[:CONTAINS]->(sl:SegmentList)
CREATE (tg:TunnelGroup)-[:CONTAINS]->(t:Tunnel)
```

**提供关系**
```cypher
CREATE (t:Tunnel)-[:PROVIDES]->(ti:TunnelInterface)
CREATE (si:ServiceInstance)-[:PROVIDES]->(sap:ServiceAccessPoint)
```

**配置关系**
```cypher
CREATE (t:Tunnel)-[:CONFIGURED_WITH]->(tp:TunnelProtection)
CREATE (t:Tunnel)-[:USES]->(tt:TunnelTemplate)
CREATE (tt:TunnelTemplate)-[:SERVES]->(t:Tunnel)
CREATE (t:Tunnel)-[:BELONGS_TO]->(te:TEClass)
```

**引用关系**
```cypher
CREATE (tp:TunnelPath)-[:REFERENCES]->(ep:ExplicitPath)
CREATE (ep:ExplicitPath)-[:REFERENCED_BY]->(tp:TunnelPath)
CREATE (tp:TunnelPath)-[:COMPUTED_BY]->(pc:PathComputation)
CREATE (pc:PathComputation)-[:COMPUTES]->(tp:TunnelPath)
```

**关联关系**
```cypher
CREATE (lsp:LSP)-[:ASSOCIATED_WITH]->(rs:RSVPTESession)
CREATE (tp:TunnelProtection)-[:PROTECTS]->(tp2:TunnelPath)
CREATE (tp2:TunnelPath)-[:PROTECTED_BY]->(tp:TunnelProtection)
```

**冗余关系**
```cypher
CREATE (tp1:TunnelPath)-[:PRIMARY_BACKUP]->(tp2:TunnelPath)
CREATE (lsp1:LSP)-[:PRIMARY_BACKUP]->(lsp2:LSP)
CREATE (t1:Tunnel)-[:PROTECTS]->(t2:Tunnel)
```

**对等关系**
```cypher
CREATE (t1:Tunnel)-[:PEERS_WITH]->(t2:Tunnel)
CREATE (srp1:SRPolicy)-[:CANDIDATE_WITH]->(srp2:SRPolicy)
CREATE (n1:Node)-[:TUNNEL_ENDPOINT]->(n2:Node)
```

**短接关系(Shortcut)**
```cypher
CREATE (n:Node)-[:HAS_TUNNEL]->(t:Tunnel)
CREATE (n:Node)-[:HAS_SR_POLICY]->(srp:SRPolicy)
CREATE (t:Tunnel)-[:CONNECTS_NODE]->(n:Node)
CREATE (tp:TunnelPath)-[:HAS_LSP]->(lsp:LSP)
```

---

#### 2.1.5 第5层:Service层内关系

**包含关系**
```cypher
CREATE (ns:NetworkService)-[:CONTAINS]->(vpn:VPNInstance)
CREATE (ns:NetworkService)-[:CONTAINS]->(evpn:EVPNInstance)
CREATE (vpn:VPNInstance)-[:CONTAINS]->(si:ServiceInstance)
CREATE (evpn:EVPNInstance)-[:CONTAINS]->(si:ServiceInstance)
CREATE (n:Node)-[:CONTAINS]->(si:ServiceInstance)
CREATE (n:Node)-[:CONTAINS]->(vni:VXLANVNI)
```

**使用关系**
```cypher
CREATE (evpn:EVPNInstance)-[:USES]->(vni:VXLANVNI)
CREATE (vni:VXLANVNI)-[:SERVES]->(evpn:EVPNInstance)
```

**提供关系**
```cypher
CREATE (si:ServiceInstance)-[:PROVIDES]->(sap:ServiceAccessPoint)
CREATE (sap:ServiceAccessPoint)-[:PROVIDED_BY]->(si:ServiceInstance)
```

**连接关系**
```cypher
CREATE (vpn1:VPNInstance)-[:CONNECTS_TO]->(vpn2:VPNInstance)
CREATE (evpn1:EVPNInstance)-[:CONNECTS_TO]->(evpn2:EVPNInstance)
```

**冗余关系**
```cypher
CREATE (si1:ServiceInstance)-[:PRIMARY_BACKUP]->(si2:ServiceInstance)
CREATE (vni1:VXLANVNI)-[:ANYCAST_VTEP]->(vni2:VXLANVNI)
```

**对等关系**
```cypher
CREATE (sap1:ServiceAccessPoint)-[:PEERS_WITH]->(sap2:ServiceAccessPoint)
CREATE (vpn:VPNInstance)-[:INTERWORKS_WITH]->(evpn:EVPNInstance)
```

**短接关系(Shortcut)**
```cypher
CREATE (n:Node)-[:HAS_VPN]->(vpn:VPNInstance)
CREATE (n:Node)-[:HAS_EVPN]->(evpn:EVPNInstance)
CREATE (vpn:VPNInstance)-[:HAS_SAP]->(sap:ServiceAccessPoint)
CREATE (evpn:EVPNInstance)-[:HAS_SAP]->(sap:ServiceAccessPoint)
CREATE (ns:NetworkService)-[:HAS_SAP]->(sap:ServiceAccessPoint)
```

---

#### 2.1.6 第6层:Business层内关系

**包含关系**
```cypher
CREATE (bs:BusinessService)-[:CONTAINS]->(bss:BaseStationService)
CREATE (bs:BusinessService)-[:CONTAINS]->(ell:EnterpriseLeasedLine)
CREATE (bss:BaseStationService)-[:BELONGS_TO]->(bs:BusinessService)
CREATE (ell:EnterpriseLeasedLine)-[:BELONGS_TO]->(bs:BusinessService)
```

---

### 2.2 层间关系(Inter-Layer)

#### 2.2.1 Business层 → Service层

**使用关系**
```cypher
CREATE (bs:BusinessService)-[:USES]->(ns:NetworkService)
CREATE (ns:NetworkService)-[:SERVES]->(bs:BusinessService)

CREATE (bs:BusinessService)-[:USES_VPN]->(vpn:VPNInstance)
CREATE (bs:BusinessService)-[:USES_EVPN]->(evpn:EVPNInstance)
```

**交付关系**
```cypher
CREATE (ell:EnterpriseLeasedLine)-[:DELIVERED_BY]->(si:ServiceInstance)
CREATE (si:ServiceInstance)-[:DELIVERS]->(ell:EnterpriseLeasedLine)

CREATE (bss:BaseStationService)-[:DELIVERED_BY]->(si:ServiceInstance)
CREATE (si:ServiceInstance)-[:DELIVERS]->(bss:BaseStationService)
```

---

#### 2.2.2 Service层 → Tunnel层

**承载关系**
```cypher
CREATE (ns:NetworkService)-[:CARRIED_BY]->(t:Tunnel)
CREATE (t:Tunnel)-[:CARRIES]->(ns:NetworkService)

CREATE (vpn:VPNInstance)-[:CARRIED_BY_TUNNEL]->(t:Tunnel)
CREATE (t:Tunnel)-[:CARRIES_VPN]->(vpn:VPNInstance)

CREATE (evpn:EVPNInstance)-[:CARRIED_BY_SR]->(srp:SRPolicy)

CREATE (si:ServiceInstance)-[:USES_TUNNEL]->(t:Tunnel)
CREATE (t:Tunnel)-[:SERVES_INSTANCE]->(si:ServiceInstance)
```

---

#### 2.2.3 Tunnel层 → Routing层

**依赖关系**
```cypher
CREATE (t:Tunnel)-[:RELIES_ON]->(ri:RoutingInstance)
CREATE (ri:RoutingInstance)-[:SUPPORTS]->(t:Tunnel)

CREATE (t:Tunnel)-[:USES_BGP]->(bgp:BGPProcess)

CREATE (srp:SRPolicy)-[:RELIES_ON_ROUTING]->(ri:RoutingInstance)

CREATE (tp:TunnelPath)-[:FOLLOWS_ROUTE]->(r:Route)
CREATE (r:Route)-[:GUIDES_PATH]->(tp:TunnelPath)
```

---

#### 2.2.4 Routing层 → Link层

**使用关系**
```cypher
CREATE (ri:RoutingInstance)-[:USES_LINK]->(l:Link)
CREATE (l:Link)-[:PROVIDES_PATH]->(ri:RoutingInstance)

CREATE (r:Route)-[:USES_LINK]->(l:Link)
CREATE (l:Link)-[:CARRIES_ROUTE]->(r:Route)

CREATE (ri:RoutingInstance)-[:USES_VLAN]->(vlan:VLAN)

CREATE (bgp:BGPProcess)-[:ESTABLISHED_OVER]->(l:Link)
CREATE (l:Link)-[:CARRIES_BGP]->(bgp:BGPProcess)

CREATE (isis:IS-ISProcess)-[:ESTABLISHED_OVER]->(l:Link)
```

---

#### 2.2.5 Link层 → Network层

**连接关系**
```cypher
CREATE (l:Link)-[:CONNECTS]->(n:Node)
CREATE (n:Node)-[:PROVIDES_LINK]->(l:Link)

CREATE (l:Link)-[:TERMINATES_AT]->(pp:PhysicalPort)
CREATE (pp:PhysicalPort)-[:TERMINATES_LINK]->(l:Link)

CREATE (lag:LAG)-[:AGGREGATES]->(pp:PhysicalPort)
CREATE (pp:PhysicalPort)-[:AGGREGATED_BY]->(lag:LAG)

CREATE (vlan:VLAN)-[:CARRIED_ON]->(pp:PhysicalPort)

CREATE (tp:TerminationPoint)-[:MAPS_TO]->(pp:PhysicalPort)
CREATE (pp:PhysicalPort)-[:PROVIDES_TP]->(tp:TerminationPoint)

CREATE (li:LogicalInterface)-[:BASED_ON]->(pp:PhysicalPort)
CREATE (pp:PhysicalPort)-[:PROVIDES_INTERFACE]->(li:LogicalInterface)

CREATE (si:SubInterface)-[:BASED_ON]->(pp:PhysicalPort)

CREATE (l:Link)-[:CARRIED_BY_FIBER]->(of:OpticalFiber)
```

---

### 2.3 跨层短接关系(Cross-Layer Shortcut)

#### 2.3.1 自顶向下跨层短接关系

**Business层 → Network层**
```cypher
CREATE (bs:BusinessService)-[:USES_PORT]->(pp:PhysicalPort)
CREATE (bs:BusinessService)-[:SERVES_SITE]->(s:Site)
CREATE (bs:BusinessService)-[:USES_NODE]->(n:Node)

CREATE (ell:EnterpriseLeasedLine)-[:USES_PORT]->(pp:PhysicalPort)
CREATE (bss:BaseStationService)-[:USES_PORT]->(pp:PhysicalPort)
CREATE (bss:BaseStationService)-[:CONNECTS_NODE]->(n:Node)
```

**Service层 → Network层**
```cypher
CREATE (vpn:VPNInstance)-[:USES_PORT]->(pp:PhysicalPort)
CREATE (vpn:VPNInstance)-[:HAS_NODE]->(n:Node)
CREATE (evpn:EVPNInstance)-[:USES_PORT]->(pp:PhysicalPort)
CREATE (ns:NetworkService)-[:USES_PORT]->(pp:PhysicalPort)
```

**Tunnel层 → Network层**
```cypher
CREATE (t:Tunnel)-[:USES_PORT]->(pp:PhysicalPort)
CREATE (t:Tunnel)-[:CONNECTS_NODE]->(n:Node)
CREATE (srp:SRPolicy)-[:USES_PORT]->(pp:PhysicalPort)
```

---

#### 2.3.2 自底向上跨层短接关系

**Network层 → Business层**
```cypher
CREATE (pp:PhysicalPort)-[:SERVES_BUSINESS]->(bs:BusinessService)
CREATE (s:Site)-[:HOSTS_BUSINESS]->(bs:BusinessService)
CREATE (n:Node)-[:SERVES_BUSINESS]->(bs:BusinessService)

CREATE (pp:PhysicalPort)-[:SERVES_LEASED_LINE]->(ell:EnterpriseLeasedLine)
CREATE (pp:PhysicalPort)-[:SERVES_BASE_STATION]->(bss:BaseStationService)
```

**Network层 → Service层**
```cypher
CREATE (pp:PhysicalPort)-[:SERVES_VPN]->(vpn:VPNInstance)
CREATE (pp:PhysicalPort)-[:SERVES_EVPN]->(evpn:EVPNInstance)
CREATE (n:Node)-[:HOSTS_VPN]->(vpn:VPNInstance)
CREATE (s:Site)-[:HOSTS_VPN]->(vpn:VPNInstance)
```

**Network层 → Tunnel层**
```cypher
CREATE (pp:PhysicalPort)-[:SERVES_TUNNEL]->(t:Tunnel)
CREATE (n:Node)-[:HOSTS_TUNNEL]->(t:Tunnel)
```

---

## 三、实体类型汇总表

| 层级 | 层名称 | 实体类型 | IETF拓扑类型 | 主要用途 |
|-----|-------|---------|-------------|---------|
| 1 | NetworkLayer | Node | Node | 网络节点设备 |
| 1 | NetworkLayer | Site | Network | 站点位置 |
| 1 | NetworkLayer | Rack | Node | 机架 |
| 1 | NetworkLayer | Shelf | Node | 子架 |
| 1 | NetworkLayer | Board | Node | 板卡 |
| 1 | NetworkLayer | PhysicalPort | TP | 物理端口 |
| 1 | NetworkLayer | OpticalModule | TP | 光模块 |
| 1 | NetworkLayer | OpticalFiber | Link | 光纤 |
| 1 | NetworkLayer | Fan | Node | 风扇 |
| 2 | LinkLayer | Link | Link | 链路 |
| 2 | LinkLayer | LAG | Link | 链路聚合组 |
| 2 | LinkLayer | VLAN | Network | 虚拟局域网 |
| 2 | LinkLayer | TerminationPoint | TP | 终结点 |
| 2 | LinkLayer | LogicalInterface | TP | 逻辑接口 |
| 2 | LinkLayer | SubInterface | TP | 子接口 |
| 2 | LinkLayer | LoopbackInterface | TP | 环回接口 |
| 2 | LinkLayer | VLANInterface | TP | VLAN接口 |
| 2 | LinkLayer | BridgeDomain | Network | 桥接域 |
| 3 | RoutingLayer | RoutingInstance | Network | 路由实例 |
| 3 | RoutingLayer | Route | Link | 路由条目 |
| 3 | RoutingLayer | OSPFArea | Network | OSPF区域 |
| 3 | RoutingLayer | BGPProcess | Node | BGP进程 |
| 3 | RoutingLayer | IS-ISProcess | Node | IS-IS进程 |
| 3 | RoutingLayer | RoutePolicy | Node | 路由策略 |
| 3 | RoutingLayer | StaticRoute | Link | 静态路由 |
| 4 | TunnelLayer | Tunnel | Network | 隧道实例 |
| 4 | TunnelLayer | TunnelTemplate | Node | 隧道模板 |
| 4 | TunnelLayer | TunnelPath | Link | 隧道路径 |
| 4 | TunnelLayer | LSP | Link | 标签交换路径 |
| 4 | TunnelLayer | SRPolicy | Link | SR策略 |
| 4 | TunnelLayer | TunnelInterface | TP | 隧道接口 |
| 4 | TunnelLayer | TunnelProtection | Node | 隧道保护 |
| 4 | TunnelLayer | TEClass | Network | TE类 |
| 4 | TunnelLayer | ExplicitPath | Network | 显式路径 |
| 4 | TunnelLayer | SegmentList | Link | Segment列表 |
| 4 | TunnelLayer | RSVPTESession | Node | RSVP-TE会话 |
| 4 | TunnelLayer | PathComputation | Node | 路径计算 |
| 4 | TunnelLayer | TunnelGroup | Network | 隧道组 |
| 5 | ServiceLayer | VPNInstance | Network | VPN实例 |
| 5 | ServiceLayer | EVPNInstance | Network | EVPN实例 |
| 5 | ServiceLayer | NetworkService | Network | 网络服务 |
| 5 | ServiceLayer | ServiceInstance | Node | 服务实例 |
| 5 | ServiceLayer | VXLANVNI | TP | VXLAN VNI |
| 5 | ServiceLayer | ServiceAccessPoint | TP | 服务接入点 |
| 6 | BusinessLayer | BusinessService | Network | 业务服务 |
| 6 | BusinessLayer | BaseStationService | Network | 基站业务 |
| 6 | BusinessLayer | EnterpriseLeasedLine | Network | 企业专线 |

---

## 四、关系类型汇总表

### 4.1 层内关系类型

| 关系类型 | 方向 | 适用实体 | 语义 |
|---------|------|---------|------|
| CONTAINS | 单向/双向 | 层级结构实体 | 包含关系 |
| BELONGS_TO | 双向 | 层级结构实体 | 属于关系(CONTAINS反向) |
| CONNECTS_TO | 双向 | 端口、链路、光纤 | 连接关系 |
| CONNECTED_BY | 双向 | 链路、光纤 | 被连接关系(CONNECTS_TO反向) |
| PROVIDES | 单向/双向 | 端口、接口 | 提供关系 |
| PROVIDED_BY | 双向 | 接口、终结点 | 被提供关系(PROVIDES反向) |
| MEMBER_OF | 双向 | 端口、LAG | 成员关系 |
| HAS_MEMBER | 双向 | LAG | 拥有成员关系(MEMBER_OF反向) |
| AGGREGATES | 双向 | LAG、链路 | 聚合关系 |
| AGGREGATED_BY | 双向 | 链路、端口 | 被聚合关系(AGGREGATES反向) |
| RUNS | 单向 | 路由实例、进程 | 运行关系 |
| GENERATES | 单向/双向 | 进程、路由 | 生成关系 |
| GENERATED_BY | 双向 | 路由 | 被生成关系(GENERATES反向) |
| APPLIES | 单向/双向 | 进程、策略 | 应用关系 |
| APPLIED_BY | 双向 | 策略 | 被应用关系(APPLIES反向) |
| MATCHES | 单向 | 路由、策略 | 匹配关系 |
| HOSTS | 单向 | 节点、隧道 | 承载关系 |
| USES | 单向/双向 | 队道、模板、VNI | 使用关系 |
| SERVES | 双向 | 模板、VNI | 服务关系(USES反向) |
| CONFIGURED_WITH | 单向 | 队道、保护 | 配置关系 |
| REFERENCES | 双向 | 路径、显式路径 | 引用关系 |
| REFERENCED_BY | 双向 | 显式路径 | 被引用关系(REFERENCES反向) |
| COMPUTED_BY | 双向 | 路径、计算 | 计算关系 |
| COMPUTES | 双向 | 路径计算 | 计算关系(COMPUTED_BY反向) |
| ASSOCIATED_WITH | 单向 | LSP、会话 | 关联关系 |
| PROTECTS | 单向/双向 | 保护、路径 | 保护关系 |
| PROTECTED_BY | 双向 | 路径 | 被保护关系(PROTECTS反向) |
| PRIMARY_BACKUP | 单向/双向 | 冗余实体 | 主备关系 |
| STACKS_WITH | 双向 | 节点 | 堆叠关系 |
| BGP_NEIGHBOR | 双向 | BGP进程 | BGP邻居关系 |
| ISIS_NEIGHBOR | 双向 | IS-IS进程 | IS-IS邻居关系 |
| PEER_WITH | 双向 | 实例、桥接域 | 对等关系 |
| PEERS_WITH | 双向 | 接入点、队列 | 对等关系 |
| ECMP_WITH | 双向 | 路由 | 等价路由关系 |
| CANDIDATE_WITH | 双向 | SR策略 | 候选路径关系 |
| PARALLEL_WITH | 双向 | 链路 | 平行链路关系 |
| CROSS_DEVICE_LAG | 双向 | LAG | 跨设备LAG关系 |
| INTERWORKS_WITH | 双向 | VPN、EVPN | 互操作关系 |
| ANYCAST_VTEP | 双向 | VNI | Anycast VTEP关系 |
| TUNNEL_ENDPOINT | 双向 | 节点 | 队道端点关系 |

### 4.2 层内短接关系类型

| 关系类型 | 适用层级 | 源实体 | 目标实体 | 语义 |
|---------|---------|--------|---------|------|
| HAS_PORT | Network | Node | PhysicalPort | 节点直达端口 |
| HAS_MODULE | Network | Node | OpticalModule | 节点直达光模块 |
| CONNECTS_FIBER | Network | Node | OpticalFiber | 节点直达光纤 |
| HAS_NODE | Network | Site | Node | 站点直达节点 |
| CONNECTS_LINK | Link | Node | Link | 节点直达链路 |
| HAS_VLAN | Link | Node | VLAN | 节点直达VLAN |
| CARRIES_VLAN | Link | Link | VLAN | 链路承载VLAN |
| HAS_ROUTE | Routing | Node | Route | 节点直达路由 |
| HAS_BGP | Routing | Node | BGPProcess | 节点直达BGP |
| HAS_ISIS | Routing | Node | IS-ISProcess | 节点直达IS-IS |
| BELONGS_TO_AREA | Routing | Route | OSPFArea | 路由直达区域 |
| HAS_TUNNEL | Tunnel | Node | Tunnel | 节点直达队列 |
| HAS_SR_POLICY | Tunnel | Node | SRPolicy | 节点直达SR策略 |
| CONNECTS_NODE | Tunnel | Tunnel | Node | 队列直达节点 |
| HAS_LSP | Tunnel | TunnelPath | LSP | 路径直达LSP |
| HAS_VPN | Service | Node | VPNInstance | 节点直达VPN |
| HAS_EVPN | Service | Node | EVPNInstance | 节点直达EVPN |
| HAS_SAP | Service | VPN/EVPN/NetworkService | ServiceAccessPoint | 实例直达接入点 |

### 4.3 层间关系类型

| 关系类型 | 层间方向 | 源实体 | 目标实体 | 语义 |
|---------|---------|--------|---------|------|
| USES | Business→Service | BusinessService | NetworkService | 业务使用网络服务 |
| SERVES | Service→Business | NetworkService | BusinessService | 网络服务服务业务(USES反向) |
| USES_VPN | Business→Service | BusinessService | VPNInstance | 业务直达VPN |
| USES_EVPN | Business→Service | BusinessService | EVPNInstance | 业务直达EVPN |
| DELIVERED_BY | Business→Service | BaseStation/EnterpriseLeasedLine | ServiceInstance | 业务由服务实例交付 |
| DELIVERS | Service→Business | ServiceInstance | BaseStation/EnterpriseLeasedLine | 服务实例交付业务(DELIVERED_BY反向) |
| CARRIED_BY | Service→Tunnel | NetworkService | Tunnel | 网络服务由队列承载 |
| CARRIES | Tunnel→Service | Tunnel | NetworkService | 队列承载网络服务(CARRIED_BY反向) |
| CARRIED_BY_TUNNEL | Service→Tunnel | VPNInstance | Tunnel | VPN由队列承载 |
| CARRIES_VPN | Tunnel→Service | Tunnel | VPNInstance | 队列承载VPN(CARRIED_BY_TUNNEL反向) |
| CARRIED_BY_SR | Service→Tunnel | EVPNInstance | SRPolicy | EVPN由SR策略承载 |
| USES_TUNNEL | Service→Tunnel | ServiceInstance | Tunnel | 服务实例使用队列 |
| SERVES_INSTANCE | Tunnel→Service | Tunnel | ServiceInstance | 队列服务实例(USES_TUNNEL反向) |
| RELIES_ON | Tunnel→Routing | Tunnel | RoutingInstance | 队列依赖路由实例 |
| SUPPORTS | Routing→Tunnel | RoutingInstance | Tunnel | 路由实例支撑队列(RELIES_ON反向) |
| USES_BGP | Tunnel→Routing | Tunnel | BGPProcess | 队列使用BGP |
| RELIES_ON_ROUTING | Tunnel→Routing | SRPolicy | RoutingInstance | SR策略依赖路由 |
| FOLLOWS_ROUTE | Tunnel→Routing | TunnelPath | Route | 队列路径遵循路由 |
| GUIDES_PATH | Routing→Tunnel | Route | TunnelPath | 路由引导队列路径(FOLLOWS_ROUTE反向) |
| USES_LINK | Routing→Link | RoutingInstance/Route | Link | 路由使用链路 |
| PROVIDES_PATH | Link→Routing | Link | RoutingInstance | 链路提供路径(USES_LINK反向) |
| CARRIES_ROUTE | Link→Routing | Link | Route | 链路承载路由(USES_LINK反向) |
| USES_VLAN | Routing→Link | RoutingInstance | VLAN | 路由实例使用VLAN |
| ESTABLISHED_OVER | Routing→Link | BGPProcess/IS-ISProcess | Link | 路由协议建立在链路上 |
| CARRIES_BGP | Link→Routing | Link | BGPProcess | 链路承载BGP(ESTABLISHED_OVER反向) |
| CONNECTS | Link→Network | Link | Node | 链路连接节点 |
| PROVIDES_LINK | Network→Link | Node | Link | 节点提供链路(CONNECTS反向) |
| TERMINATES_AT | Link→Network | Link | PhysicalPort | 链路终结于端口 |
| TERMINATES_LINK | Network→Link | PhysicalPort | Link | 端口终结链路(TERMINATES_AT反向) |
| CARRIED_ON | Link→Network | VLAN | PhysicalPort | VLAN承载于端口 |
| MAPS_TO | Link→Network | TerminationPoint | PhysicalPort | 终结点映射到端口 |
| PROVIDES_TP | Network→Link | PhysicalPort | TerminationPoint | 端口提供终结点(MAPS_TO反向) |
| BASED_ON | Link→Network | LogicalInterface/SubInterface | PhysicalPort | 接口基于端口 |
| PROVIDES_INTERFACE | Network→Link | PhysicalPort | LogicalInterface | 端口提供接口(BASED_ON反向) |
| CARRIED_BY_FIBER | Link→Network | Link | OpticalFiber | 链路由光纤承载 |

### 4.4 跨层短接关系类型

| 关系类型 | 方向 | 源实体 | 目标实体 | 语义 |
|---------|------|--------|---------|------|
| USES_PORT | 自顶向下 | Business/Service/Tunnel | PhysicalPort | 业务直达端口 |
| SERVES_SITE | 自顶向下 | BusinessService | Site | 业务直达站点 |
| USES_NODE | 自顶向下 | BusinessService | Node | 业务直达节点 |
| SERVES_BUSINESS | 自底向上 | PhysicalPort/Node | BusinessService | 端口/节点服务业务 |
| HOSTS_BUSINESS | 自底向上 | Site | BusinessService | 站点承载业务 |
| SERVES_LEASED_LINE | 自底向上 | PhysicalPort | EnterpriseLeasedLine | 端口服务专线 |
| SERVES_BASE_STATION | 自底向上 | PhysicalPort | BaseStationService | 端口服务基站 |
| SERVES_VPN | 自底向上 | PhysicalPort | VPNInstance | 端口服务VPN |
| SERVES_EVPN | 自底向上 | PhysicalPort | EVPNInstance | 端口服务EVPN |
| HOSTS_VPN | 自底向上 | Node/Site | VPNInstance | 节点/站点承载VPN |
| SERVES_TUNNEL | 自底向上 | PhysicalPort | Tunnel | 端口服务队列 |
| HOSTS_TUNNEL | 自底向上 | Node | Tunnel | 节点承载队列 |
