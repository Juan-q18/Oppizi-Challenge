# Oppizi Manual Testing Assignment
## Scenario: Route Conflict Detection and Reassignment Auditing

---

## 1. Test Design (Behavior Specifications)
The following test suite uses Cucumber Gherkin syntax to define functional, negative, edge, and permission-related business behaviors. These scenarios are designed to be fully documentation-complete for manual testers.


Feature: Route Conflict Detection and Reassignment Auditing
  As a Campaign Manager
  I want to reassign routes across campaigns while enforcing scheduling, location, and timing constraints
  So that field operations run smoothly without conflicts and changes are fully traceable

  Background:
    Given a campaign manager is logged into the admin dashboard
    And a route "R-101" exists in an upcoming campaign with location type "Outdoor"
    And the campaign start date is in 5 days
    And "Agent John" is currently assigned to route "R-101"

  # --- FUNCTIONAL / POSITIVE SCENARIOS ---

  Scenario: Successful route reassignment when all conditions are met
    Given "Agent Sarah" is available during the route schedule
    And "Agent Sarah" supports "Outdoor" location types
    When the manager reassigns route "R-101" to "Agent Sarah"
    Then the system should confirm the reassignment successfully
    And the route "R-101" should be locked from further changes for 24 hours

  Scenario: Location types must match for successful assignment
    Given a route "R-202" exists with location type "Indoor"
    And "Agent Sarah" supports "Indoor" location types
    And "Agent Sarah" is assigned to route "R-202"
    When the manager reassigns route "R-202" to another agent who supports "Indoor" locations
    Then the system should allow the reassignment

  # --- NEGATIVE SCENARIOS ---

  Scenario: Block reassignment if the campaign has already started
    Given the campaign start date was 2 days ago
    When the manager attempts to reassign route "R-101" to "Agent Sarah"
    Then the system should deny the reassignment
    And show an error message "Routes cannot be reassigned after the campaign has started"

  Scenario: Block reassignment if the receiving agent has a schedule overlap
    Given "Agent Sarah" is already assigned to a route on the same day from 10:00 to 14:00
    And route "R-101" is scheduled on that day from 11:00 to 13:00
    When the manager attempts to reassign route "R-101" to "Agent Sarah"
    Then the system should deny the reassignment
    And show an error message "Receiving agent has an overlapping route conflict"

  Scenario: Block reassignment if location types do not match
    Given "Agent Mike" only supports "Indoor" location types
    When the manager attempts to reassign the "Outdoor" route "R-101" to "Agent Mike"
    Then the system should deny the reassignment
    And show an error message "Location type mismatch for the selected agent"

  Scenario: Block immediate secondary reassignment due to the 24-hour lock
    Given "Agent Sarah" is available and matches the route criteria
    And the manager has successfully reassigned route "R-101" to "Agent Sarah"
    When the manager attempts to reassign route "R-101" from "Agent Sarah" to "Agent Alex" 10 minutes later
    Then the system should deny the reassignment
    And show an error message "This route is currently locked for adjustments"

  # --- AUDIT & NOTIFICATION SCENARIOS ---

  Scenario: Reassignment triggers audit logging and notification emails
    Given "Agent Sarah" is available and matches the route criteria
    When the manager reassigns route "R-101" to "Agent Sarah"
    Then an audit log entry should be created with the event "route.reassigned"
    And the audit log metadata should record "Agent John" as the previous agent and "Agent Sarah" as the new agent
    And a confirmation email should be sent to "Agent John" notifying them of the cancellation
    And a confirmation email should be sent to "Agent Sarah" notifying them of their new assignment

  # --- PERMISSIONS SCENARIOS ---

  Scenario Outline: Only authorized roles can perform route reassignments
    Given a user logs in with the role "<Role>"
    When the user attempts to reassign route "R-101" to an available agent
    Then the system action should result in "<Outcome>"

    Examples:
      | Role             | Outcome |
      | Admin            | Success |
      | Campaign Manager | Success |
      | Field Agent      | Denied  |
      | Guest Observer   | Denied  |