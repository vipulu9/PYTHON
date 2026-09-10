The provisioning plan for **dnn_provisioning** is ready for review.

**Service parameters:**
  • detected_ip_modes: ['static_pool']
  • ipAllocationMode: static_pool
  • scenario: static
  • radius: shared
  • account: testing
  • dnn_name: testing
  • servicename: testing
  • customer_address: testing
  • epg1: epg1-id
  • smf1: smf1-id
  • operation: create
  • servicetype: mpn::dnn
  • has_lpgs: False
  • has_epg2: False
  • dnn_snssai: 1-000100
  • slice_size: 1
  • static_ip_pool: static-ip-pool-testing
  • pdp_creation: unblocked
  • configure_PEs_SB: True
  • dnn_pcc_rule_activate: False
  • radius_server: radius_b2b
  • dnn_n7_profile: n7-1
  • dnn_n40_profile: n40-1
  • dnn_rule_space_default: rule-space-default
  • dnn_policy_control: rule-space-default
  • auth_legacy_user_info: True
  • dnn_network_instance: testing-network-instance
  • dnn_ip_condition: condition-testing
  • dnn_upf_condition: testing-upf-condition
  • dnn_local_rule_space_default: local-rule-space-default-testing
  • dnn_pcc_rule: pcc-rule-block-ue2ue-testing

**Resolved infrastructure resources:**
  • smf: smf1-id
  • epg: epg1-id
  • lpg: lpg1-id
  • epg1: epg1-id
  • smf1: smf1-id

Reply **approve** to proceed with execution, or **reject** to cancel. You can also send a JSON object with field updates, for example: {"config_payload": {"dnn_name": "new-name"}}.
