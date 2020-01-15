# tracker_merge
Merge master/slave device trackers and generate mabox view for them.
## Example configuration.yaml
```yaml
automation:
  - alias: track_merge
    initial_state: off
    trigger:
      - platform: state
        entity_id: device_tracker.xxx
      - platform: state
        entity_id: device_tracker.google_maps_xxx
    action:
      - service: python_script.device_tracker_merge
        data_template:
          device_name: 'new_device'
          master_device: device_tracker.xxx
          slave_device: device_tracker.google_maps_xxx
          trigger_id: '{{trigger.entity_id}}'
          time: 10
          force_update: 10
          accuracy: 1000
          distance: "{{ distance(states.device_tracker.xxx, states.device_tracker.google_maps_xxx) }}"

camera:
  name: tracker
  platform: generic
  still_image_url: >-
    {%- set pitch = state_attr('sensor.view_new_device', 'pitch') -%}
    {%- set c_longitude = state_attr('sensor.view_new_device', 'c_longitude') -%}
    {%- set c_latitude = state_attr('sensor.view_new_device', 'c_latitude') -%}
    {%- set o_longitude = state_attr('sensor.view_new_device', 'o_longitude') -%}
    {%- set o_latitude = state_attr('sensor.view_new_device', 'o_latitude') -%}
    {%- set m_longitude = state_attr('sensor.view_new_device', 'm_longitude') -%}
    {%- set m_latitude = state_attr('sensor.view_new_device', 'm_latitude') -%}
    {%- set pitch = state_attr('sensor.view_new_device', 'pitch') -%}
    {%- set zoom = state_attr('sensor.view_new_device', 'zoom') -%}
    {%- set bearing = state_attr('sensor.view_new_device', 'bearing') -%}
    {%- set key = states('sensor.mapbox_key') -%}
    {%- if zoom == 'auto' -%}
      https://api.mapbox.com/styles/v1/mapbox/outdoors-v10/static/pin-s-o+2ecc71({{o_longitude}},{{o_latitude}}),pin-s-c+3498db({{c_longitude}},{{c_latitude}})/auto/400x400?access_token={{key}}
    {%- else -%}
      https://api.mapbox.com/styles/v1/mapbox/outdoors-v10/static/pin-s-o+2ecc71({{o_longitude}},{{o_latitude}}),pin-s-c+3498db({{c_longitude}},{{c_latitude}})/{{m_longitude}},{{m_latitude}},{{zoom}},{{bearing}},{{pitch}}/400x400?access_token={{key}}
    {%- endif -%}
  limit_refetch_to_url_change: true
```
![Screenshot](https://github.com/kodi1/tracker_merge/blob/master/images/tracker_view.png?raw=true)

[![hacs_badge](https://img.shields.io/badge/HACS-Default-orange.svg?style=for-the-badge)](https://github.com/custom-components/hacs)
