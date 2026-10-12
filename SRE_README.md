# Senior SRE / DevOps Interview Notes

## Quick Revision Guide: Core SRE Concepts

This README explains essential Site Reliability Engineering (SRE) concepts in simple terms, with practical examples based on an online banking application.

---

## 1. SLI — Service Level Indicator

**Expansion:** Service Level Indicator

**Simple explanation:** An SLI is a measurable indicator of how well a service is performing or how reliable it is. It describes what is actually happening.

**Common examples:**
- Availability: percentage of eligible requests that succeed.
- Latency: how long requests take to complete.
- Error rate: percentage of requests that fail.
- Throughput: number of requests processed per second.

**Example:**

A banking API receives 10,000 account-balance requests. If 9,950 succeed:

`Success rate = (9,950 / 10,000) × 100 = 99.5%`

The measured 99.5% success rate is the SLI for that set of requests.

**Interview tip:** An SLI is the *measurement*, not the target. Use metrics, logs, and traces to understand what the service is doing.

---

## 2. SLO — Service Level Objective

**Expansion:** Service Level Objective

**Simple explanation:** An SLO is the reliability or performance target the team wants the service to meet, measured using one or more SLIs over a defined time window.

**Example:**

A banking team sets an SLO: at least 99.9% of eligible payment requests must succeed over a rolling 30-day period.

If the measured success rate is 99.5%, the service is below its target.

A latency SLO might say: 99% of eligible requests should complete within 500 milliseconds over the measurement window.

**Interview tip:** Define SLOs around important user journeys, such as making a payment—not only infrastructure metrics such as CPU utilization.

---

## 3. SLA — Service Level Agreement

**Expansion:** Service Level Agreement

**Simple explanation:** An SLA is a customer-facing service commitment. It can define availability or performance guarantees and the remedies that apply if the commitment is breached.

**Example:**

A company contracts to provide a banking platform with 99.5% monthly availability. The contract may specify service credits if availability falls below the agreed level.

A team might set a stricter internal SLO of 99.9% so it has an opportunity to address reliability problems before breaching the SLA.

**Interview tip:** An SLA is the *external commitment*. Its exact obligations and remedies depend on the contract.

---

## 4. Error Budget

**Expansion:** Not an acronym; it is the permitted amount of unreliability under an SLO.

**Simple explanation:** An error budget tells the team how much failure it can tolerate while still meeting its reliability objective.

**Example using time-based availability:**

For a 99.9% availability SLO over 30 days:

- Total time: 30 × 24 × 60 = 43,200 minutes.
- Allowed unavailability: 0.1%.
- Error budget: 43,200 × 0.001 = **43.2 minutes**.

If incidents consume most of that budget, the team might postpone risky releases, strengthen deployment safeguards, or prioritize reliability improvements according to its agreed policy.

For a request-based SLO, the budget can instead be expressed as a number of failed requests. For example, a 99.9% success-rate target across one million eligible requests permits 1,000 failed requests.

**Interview tip:** Error budgets help balance feature delivery with reliability. They are not always measured as downtime; the calculation must match the SLO definition.

---

## 5. Observability

**Expansion:** Not an acronym.

**Simple explanation:** Observability is the ability to understand a system's internal behavior from the outputs it produces. It helps engineers investigate why a system is slow, failing, or behaving unexpectedly.

### The three main signals

| Signal | What it provides | Example | Common tools |
|---|---|---|---|
| Metrics | Numeric measurements over time | API error rate is 4% | Prometheus, Grafana, CloudWatch |
| Logs | Records of individual events | Database connection timeout | ELK Stack, OpenSearch, CloudWatch Logs |
| Traces | A request's path across services | A database call takes 1.8 seconds | OpenTelemetry, Jaeger, Grafana Tempo |

**Example:**

A money-transfer request takes 2 seconds. A distributed trace shows that the database call accounts for 1.8 seconds. Logs then reveal repeated database connection timeouts. This gives the SRE team a focused place to investigate.

**Interview tip:** Monitoring helps detect known problems; observability helps investigate why they occurred. They complement each other.

---

## 6. Redundancy

**Expansion:** Not an acronym.

**Simple explanation:** Redundancy means having extra components or resources so that the failure of one component does not automatically take down the whole service.

**Example:**

Instead of running an application on one server, run multiple replicas behind a load balancer. If one replica fails, healthy replicas can continue serving requests.

**Common forms:**
- Multiple application instances.
- Multiple network paths.
- Replicas distributed across availability zones.
- Database replicas or standby databases.
- A recovery environment in another region.

**Important limitation:** Multiple replicas do not guarantee high availability if all replicas share the same failure domain—for example, the same node or availability zone.

**Interview tip:** Explain which failure a redundancy design protects against and identify any remaining single points of failure.

---

## 7. Failover

**Expansion:** Not an acronym.

**Simple explanation:** Failover is the process of switching from a failed or unhealthy component to a healthy alternative.

**Example:**

A banking application uses a primary database and a standby database. If the primary fails, the supported failover mechanism promotes the standby and the application reconnects to the new primary.

**Types:**
- **Automatic failover:** The system detects a failure and switches automatically.
- **Manual failover:** An operator initiates the switch after assessing the incident.

**Redundancy vs. failover:**
- Redundancy provides the alternative component.
- Failover switches service to that alternative.

**Interview tip:** Failover can involve detection, promotion, routing changes, and reconnection. It is not necessarily instantaneous. Recovery and failback are separate activities.

---

## 8. RTO — Recovery Time Objective

**Expansion:** Recovery Time Objective

**Simple explanation:** RTO is the target maximum time for restoring a service after a disruption.

**Example:**

A banking application becomes unavailable at 10:00 AM. If its RTO is 30 minutes, the target is to restore the service by 10:30 AM.

A short RTO may require a preconfigured standby environment and automated recovery. A longer RTO may allow restoration from backups and rebuilding infrastructure.

**Interview tip:** RTO is a target, not a guarantee. Validate it with recovery exercises and actual timings.

---

## 9. RPO — Recovery Point Objective

**Expansion:** Recovery Point Objective

**Simple explanation:** RPO is the target maximum amount of data loss the business can tolerate, expressed as a period of time.

**Example:**

A database fails at 10:00 AM and the RPO is five minutes. The recovery strategy should allow the system to recover to a data point no more than five minutes before the failure, for the defined failure scenario.

Depending on the system, meeting the target may require backups, transaction-log archiving, or database replication.

**RTO vs. RPO:**

| Concept | Main question | Example |
|---|---|---|
| RTO | How quickly must service be restored? | Restore within 30 minutes |
| RPO | How far back in time may recovered data go? | Recover to a point no more than 5 minutes before failure |

**Interview tip:** A system can meet its RTO and still fail its RPO. Measure service recovery time and data recovery point separately.

---

## 10. DR — Disaster Recovery

**Expansion:** Disaster Recovery

**Simple explanation:** Disaster recovery is the strategy, technology, and set of procedures used to restore services after a major disruption, such as a regional cloud outage, severe data corruption, or a security incident.

**Common DR approaches:**

| Approach | How it works | General cost and recovery profile |
|---|---|---|
| Backup and restore | Rebuild infrastructure and restore data from backups | Usually lower cost; recovery may be slower |
| Pilot light | Keep essential data and core components ready | Moderate cost; additional activation is needed |
| Warm standby | Maintain a smaller operational recovery environment | Higher cost; generally faster recovery |
| Multi-site active-active | Run production workloads in multiple locations | Often highest cost and complexity; can enable very fast failover |

**Example:**

If a primary cloud region becomes unavailable, the DR plan may activate a recovery region, restore or promote the database, deploy or scale application services, validate the system, and redirect traffic.

**Interview tip:** Backups alone do not prove that DR works. Test restoration, data integrity, dependencies, access, RTO, and RPO regularly.

---

## 11. Multi-Region Design

**Expansion:** Not an acronym.

**Simple explanation:** Multi-region design means deploying or maintaining application infrastructure in two or more geographically separate cloud regions to reduce dependence on a single region.

**Example:**

- Primary region: AWS Mumbai.
- Recovery region: AWS Singapore.
- Application and network resources are prepared in the recovery region.
- Database changes are replicated according to the selected data architecture.
- Health checks and a tested failover procedure help redirect traffic when required.

These regions are illustrative. A real design must consider latency, data residency, compliance, service availability, and business needs.

### Common patterns

**Active-passive**
- One region serves normal production traffic.
- Another region is ready for recovery.
- Often simpler than active-active, but failover and data promotion may take time.

**Active-active**
- Multiple regions serve production traffic.
- Can improve regional resilience and user latency.
- Requires careful handling of data consistency, conflicts, split-brain risks, deployments, and operational complexity.

**Pilot light / warm standby**
- The recovery region has essential components or a smaller running environment.
- Resources are activated or scaled during a disaster.

**Interview tip:** A second region alone does not guarantee a successful DR strategy. Confirm that data replication, dependencies, traffic routing, security, capacity, and recovery procedures meet the required RTO and RPO.

---

## 12. How the Concepts Work Together During an Incident

Imagine the banking application's primary region becomes unavailable.

1. **SLI:** Monitoring shows that successful customer requests have dropped.
2. **SLO:** The team assesses whether the reliability target is being breached.
3. **Observability:** Metrics, logs, traces, and cloud health signals help isolate the problem.
4. **Redundancy:** A separate recovery environment provides alternative capacity.
5. **Failover:** The recovery process activates the alternative environment and redirects traffic.
6. **RTO:** The team measures how long service restoration takes against the target.
7. **RPO:** The team validates whether the recovered data meets the permitted data-loss target.
8. **Error budget:** The team evaluates the incident's impact on the applicable SLO.
9. **Disaster recovery:** The team follows recovery and validation procedures and later plans a safe failback.
10. **Multi-region design:** The architecture provides geographic separation to reduce dependence on one region.
11. **SLA:** The team determines whether the incident affected any contractual commitments.

---

## 13. Quick Revision Table

| Concept | Full form / expansion | Simple meaning | Example |
|---|---|---|---|
| SLI | Service Level Indicator | A measurement of actual service performance or reliability | 99.95% of eligible requests succeed |
| SLO | Service Level Objective | The target for a service-level indicator | At least 99.9% of eligible requests should succeed |
| SLA | Service Level Agreement | A customer-facing service commitment, potentially with remedies | Contractual availability commitment |
| Error Budget | Not an acronym | Permitted unreliability under an SLO | 43.2 minutes for a 99.9% time-based availability SLO over 30 days |
| Observability | Not an acronym | Understanding system behavior through its outputs | Use traces to identify a slow database call |
| Redundancy | Not an acronym | Extra components that reduce single points of failure | Multiple app replicas across availability zones |
| Failover | Not an acronym | Switching to a healthy alternative | Promote a standby database |
| RTO | Recovery Time Objective | Target time to restore service | Restore within 30 minutes |
| RPO | Recovery Point Objective | Target maximum data loss expressed as time | Recover to a point no more than 5 minutes before failure |
| DR | Disaster Recovery | Procedures and technology to restore service after a major disruption | Recover after a regional outage |
| Multi-Region Design | Not an acronym | Infrastructure across multiple geographic cloud regions | Mumbai primary with Singapore recovery |

---

## 14. Three Interview Practice Questions

### Q1. The application is available, but customers say it is slow. Is the SLO met?

**Answer:** It depends on the SLOs defined. An availability SLO may be met while a separate latency SLO is violated. Check latency percentiles, traces, logs, and dependency metrics to identify the bottleneck.

### Q2. Two application replicas run in the same availability zone. Is the system highly available?

**Answer:** Not necessarily. Both replicas share the risk of an availability-zone outage. Distribute replicas across appropriate failure domains and verify that dependencies, storage, and routing are resilient too.

### Q3. The RTO is 30 minutes and the RPO is five minutes. Service returns after 20 minutes, but the recovered database is missing ten minutes of updates. Were both targets met?

**Answer:** The RTO was met because service returned within 30 minutes. The RPO was not met because the recovered data is missing more than the permitted five minutes of updates.

---

## Final Interview Reminder

For a Senior SRE / DevOps Engineer interview, go beyond definitions. Explain how you would measure reliability, diagnose a production incident, restore service safely, validate data recovery, and prevent recurrence.

Connect these concepts to practical experience with Kubernetes, Terraform, CI/CD, Prometheus/Grafana, cloud networking, automation, and chaos testing. Use real outcomes from your own work and avoid inventing metrics.
