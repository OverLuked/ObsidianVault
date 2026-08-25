### Fundamental approaches of Testing

##### Static Testing
*Tests without the need of code execution*
###### Methods
- Code Reviews
- Walkthroughs
- Inspection
- Static Analysis Tools
###### Benefits
- Early defect detection
- Cost-effective bug prevention
- Improves code quality
- Knowledge Sharing
##### Dynamic Testing
*Tests that requires code execution with various inputs and outputs*
###### Types
- Functional Testing
	- Testing features work correctly
- Performance Testing
	- Speed, load, stress testing
- Security Testing
	- Vulnerability assessment
- Usability Testing
	- User experience validation

###### Characteristics
- Requires Test environment
- Uses test data
- Validates actual behavior
- Can be automated

##### Static vs Dynamic Testing

| Aspect         | Static Testing              | Dynamic Testing                    |
| -------------- | --------------------------- | ---------------------------------- |
| Code Execution | No code execution           | Code is executed                   |
| When Applied   | Early development phases    | After code completion              |
| Cost           | Lower cost                  | Higher cost                        |
| Defect Types   | Logic errors, syntax issues | Runtime errors, performance issues |


### Choosing The Right Approach of Testing
##### Manual Testing
*Manually Executed Test Cases by Humans without the use of Automation Tools*
###### Best For
- Exploratory Testing
- Usability Testing
- Ad-hoc Testing
- New feature testing
- UI/UX validation

###### Limitations
- Time-consuming for repetitive Tasks
- Human error prone
- Not suitable for load testing
- Resource intensive

##### Automation Testing
*Using tools and scripts to execute tests automatically without human intervention*
###### Best For
- Regression Testing
- Performance Testing
- Repetitive test cases
- Data-driven testing
- Cross-browser testing

###### Limitations
- High initial setup cost
- Maintenance overhead
- Cannot test user experience
- Requires technical skills

##### Manual vs Automation Testing

| Aspect           | Manual Testing                     | Automation Testing                 |
| ----------------- | ------------------------------------ | ------------------------------------ |
| Execution          | Performed by humans                  | Performed by scripts/tools           |
| Speed              | Slower, especially repetitive runs   | Fast, repeatable execution           |
| Best Suited        | Exploratory, usability, ad-hoc       | Regression, performance, repetitive tests |
| Setup Cost         | Low                                   | High initial investment              |
| Maintenance        | None                                  | Requires ongoing script upkeep       |
| Human Judgement    | Can assess UX/look-and-feel           | Cannot judge subjective quality      |

## Functional Testing
*Testing what the system does*

##### What is Functional Testing?
- Verifies each function of the application operates according to the requirement specification
- Black box testing technique
- Validates business logic and input-output behavior

##### Focus Areas
- User interface functionality
- Database operations
- API functionality
- Security features
- Business workflow validation

##### Levels
- Spans Unit -> Integration -> System -> UAT - see [[QA Foundations]] `Testing Levels`

##### Sample Functional Test Case

| Field        | Value                                  |
| ------------ | ---------------------------------------- |
| Test ID      | TC_REG_001                              |
| Objective    | Verify user can register successfully   |
| Precondition | User not registered before              |
| Priority     | High                                     |

| Step | Action                        | Expected Result             |
| ---- | ------------------------------- | ------------------------------ |
| 1    | Navigate to registration page   | Registration form displayed   |
| 2    | Fill valid details and submit   | Success message shown         |
| 3    | Check email for confirmation    | Confirmation email received   |

## Testing Techniques
*White-box methods for measuring test coverage*

##### Code Coverage Formula
```
Coverage = (Number of Executed Items / Total Number of Items) x 100%
```

##### Statement Coverage
*Ensures every executable statement runs at least once*
- Weakest form of coverage
- Easy to measure

##### Branch Coverage
*Ensures every decision point (true/false) is executed at least once*
- Stronger than statement coverage
- Doesn't test every condition combination

##### Condition Coverage
*Ensures each boolean sub-expression is evaluated to both true and false*
- More thorough than branch coverage
- May require multiple test cases

##### Loop Testing
*Validates loop constructs*
- Simple Loops -> skip (n=0), one pass (n=1), two passes (n=2), typical (n=m), n-1/n/n+1
- Nested Loops -> test innermost first, minimum on outer loops, work outward
- Concatenated Loops -> test independent loops separately, dependent loops as nested

##### Path Testing
*Tests all possible paths through the code using cyclomatic complexity*
```
V(G) = Number of decision points + 1
```
- Determines the minimum number of test cases needed for full path coverage

##### Coverage Techniques Comparison

| Technique | Covers                        | Strength                     | Weakness                        |
| --------- | -------------------------------- | ------------------------------- | ---------------------------------- |
| Statement | Every executable statement        | Easy to measure                 | Weakest form of coverage           |
| Branch    | Every decision outcome            | Better than statement           | Doesn't test all conditions        |
| Condition | Every boolean condition            | Tests individual conditions      | May miss some decision outcomes    |
| Path      | Every possible execution path      | Most thorough                    | Impractical for complex code       |

##### Coverage Goals
- Critical systems -> 100% branch coverage
- Commercial software -> 80-90% coverage
- Web applications -> 70-80% coverage
- Prototypes -> 60-70% coverage

## Performance Testing
*Verifying speed, scalability, and stability under load*

##### Load Testing
*System behavior under expected normal load*
- Verifies response time requirements
- Ensures system stability
- Validates throughput expectations

##### Stress Testing
*System behavior beyond normal capacity to find the breaking point*
- Goal: graceful degradation, not a crash
- Example progression: Normal load -> Increased load -> Breaking point

##### Volume Testing
*System performance with large amounts of data*
- Large datasets, large files, high record counts
- Validates: query response time, memory usage, data integrity

##### Spike Testing
*System behavior under sudden, extreme load increases*
- Example: 5,000 -> 50,000 users in 2 minutes
- Goal: verify the system survives a sudden traffic surge without failing completely

##### Performance Benchmarks

| Category           | Metric               | Target       |
| -------------------- | ----------------------- | -------------- |
| Web Applications       | Page Load               | < 3 seconds   |
| Web Applications       | API Response             | < 200ms       |
| Web Applications       | Database Query           | < 100ms       |
| Mobile Apps            | App Launch               | < 2 seconds   |
| Mobile Apps            | Screen Transition          | < 1 second    |
| Mobile Apps            | Data Sync                | < 5 seconds   |
| Enterprise Systems      | Transaction              | < 500ms       |
| Enterprise Systems      | Report Generation          | < 30 sec      |
| Enterprise Systems      | System Availability        | 99.9%         |

## Non-Functional Testing
*Testing how the system performs, rather than what it does*

##### Quality Attributes
- Usability
- Reliability
- Security
- Compatibility
- Performance - see `Performance Testing` above

##### Security Testing
*Validates the system protects data and resists attack*
- SQL Injection
- Cross-site scripting (XSS)
- Authentication bypass
- Session management
- Data encryption validation

##### Usability Testing
*Evaluates ease of use from a real user's perspective*
- Ease of navigation
- Interface clarity
- Task completion time
- Error prevention
- User satisfaction

##### Compatibility Testing
*Verifies consistent behavior across environments*
- Browsers -> Chrome, Firefox, Safari, Edge
- Devices -> phones, tablets, desktop resolutions
- OS versions

##### Reliability Testing
*Measures system stability over time*
- MTBF - Mean Time Between Failures
- MTTR - Mean Time To Recovery
- Availability target (e.g. 99.9% uptime)
- Failure rate threshold (e.g. < 0.1% of transactions)

## API Testing
*Testing the data exchange and business logic layer directly, without a UI*

##### What is API Testing?
- Tests APIs directly and as part of integration testing
- Validates functionality, reliability, performance, and security of data exchange
##### Why It Matters
- Faster execution than UI tests
- Early detection of integration issues
- Independent of frontend changes

##### HTTP Methods

| Method  | Purpose                      | Idempotent | Notes                            |
| -------- | ------------------------------- | ------------ | ----------------------------------- |
| GET      | Retrieve data                  | Yes          | No body, safe to repeat, cacheable |
| POST     | Create a resource                | No           | Request body required              |
| PUT      | Replace a resource                | Yes          | Requires full resource data        |
| PATCH    | Partially update a resource        | No           | Only changed fields required       |
| DELETE   | Remove a resource                 | Yes          | Safe to repeat                     |

##### Common HTTP Status Codes

| Code | Meaning                | Usage                              |
| ---- | ------------------------ | ------------------------------------- |
| 200  | OK                       | Successful GET/PUT/PATCH             |
| 201  | Created                  | Successful POST                      |
| 204  | No Content               | Successful DELETE                    |
| 400  | Bad Request              | Malformed JSON, missing fields       |
| 401  | Unauthorized             | Missing/invalid token                |
| 403  | Forbidden                | Valid auth, no permission            |
| 404  | Not Found                | Invalid endpoint/ID                  |
| 422  | Unprocessable Entity      | Valid format, business logic fails   |
| 500  | Internal Server Error      | Unexpected server issue              |
| 503  | Service Unavailable       | Maintenance or overload              |

##### Best Practices
###### Do's
- Test early and often - include API tests in CI/CD
- Validate status codes, headers, and response body
- Test negative scenarios - invalid input, missing data, edge cases
- Use environment variables instead of hardcoded URLs/keys
- Validate response structure/schema
- Clean up test data after each run
###### Don'ts
- Don't test only the happy path
- Don't hardcode test data
- Don't ignore response headers
- Don't skip authorization/security checks
- Don't rely only on status codes - check the actual response content

## Mobile Testing
*Testing across devices, OS versions, and real-world network conditions*

##### Key Testing Areas
- Network switching (Wi-Fi <-> mobile data)
- Background/foreground app behavior
- Orientation changes
- Battery and memory usage
- Device-specific hardware (camera, GPS, sensors)
- OS version differences

##### Checklist
###### Pre-Testing
- Devices charged and ready
- Test accounts created for each role
- App installed on all target devices
- Network configurations prepared (Wi-Fi, 4G, 5G)
###### Core Testing
- Network switching in both directions
- Background/foreground transitions
- Orientation changes (if supported)
- Performance (load times, transitions)
- Battery and memory usage
###### Device-Specific
- Screen size variations
- OS version differences
- Hardware features (camera, GPS)
- Manufacturer customizations
- Low storage scenarios
###### Real-World Scenarios
- Interruptions (calls, notifications)
- Poor/unstable network conditions
- Low battery / power saving mode
- Long usage sessions (memory leaks)

##### App Store Compliance
- Apple - must provide an accessible, working account deletion option or the app is rejected
- Google Play - must target a recent Android API level and declare data collection practices

##### Best Practices
###### Do's
- Test on real devices, not just emulators
- Test network transitions
- Test with low storage
- Test both portrait and landscape (if supported)
###### Don'ts
- Don't rely only on emulators
- Don't test on a single device
- Don't ignore OS updates
- Don't overlook localization (RTL languages can break layouts)

## Black Box vs White Box Testing
*Testing approaches based on how much internal code knowledge is used*

##### Black Box Testing
*Testing without knowledge of internal code structure - focused on inputs and outputs*
###### Techniques
- Equivalence Partitioning - group similar inputs
- Boundary Value Analysis - test edge values
- Decision Table Testing - test business rules
- State Transition Testing - test state changes

##### White Box Testing
*Testing with full knowledge of internal code structure, logic, and design*
###### Techniques
- Statement Coverage
- Branch Coverage
- Path Coverage
- Condition Coverage
- see `Testing Techniques` above for detail

##### Gray Box Testing
*Hybrid approach - partial knowledge of internal workings*
- Partial code knowledge, access to design documents
- Best for: Integration testing, API testing, penetration testing, regression testing

##### Comparison

| Aspect             | Black Box              | Gray Box                        | White Box                |
| -------------------- | ------------------------- | ---------------------------------- | ---------------------------- |
| Code Knowledge        | None                       | Partial                            | Full                         |
| Tester Perspective     | End user                   | Integration/API level              | Developer                    |
| Focus                 | Inputs/outputs              | Data exchange between systems       | Internal logic and paths     |
| Common Use             | Functional/UAT testing       | API/integration testing            | Unit testing, automation     |

