# API Request & Response models

```

# GraphQL Object Types
class AssessmentType(SQLAlchemyObjectType):
    class Meta:
        model = Assessment

class ResponseType(SQLAlchemyObjectType):
    class Meta:
        model = Response

class Query(ObjectType):
    assessments = List(AssessmentType, organizationId=Int(), status=String(), page=Int(), limit=Int())

    async def resolve_assessments(self, info, organizationId, status=None, page=1, limit=10):
        # Fetch assessments based on organizationId and status
        return []  # Replace with actual assessment objects

class CreateAssessmentInput(InputObjectType):
    name = String(required=True)
    organizationId = Int(required=True)
    frameworkId = Int(required=True)
    questions = List(InputObjectType)  

class CreateAssessment(Mutation):
    class Arguments:
        input = CreateAssessmentInput(required=True)

    assessment = Field(AssessmentType)

    async def mutate(self, info, input):
        # logic to create an assessment
        new_assessment = Assessment(name=input.name, status='draft')  # Replace with actual DB logic
        return CreateAssessment(assessment=new_assessment)

class SubmitResponseInput(InputObjectType):
    questionId = Int(required=True)
    sectionId = Int(required=True)
    value = String(required=True)

class SubmitResponse(Mutation):
    class Arguments:
        input = SubmitResponseInput(required=True)

    response = Field(ResponseType)

    async def mutate(self, info, input):
        # logic to save the response
        new_response = Response(question_id=input.questionId, section_id=input.sectionId, value=input.value)
        # Save to the database here...
        
        # Notify subscribers of the new response
        await websocket_manager.send_message(f"Response saved: {new_response.id}")
        return SubmitResponse(response=new_response)

class ResponseSubscription(Subscription):
    class Arguments:
        questionId = Int(required=True)

    response = Field(ResponseType)

    async def resolve(self, info, questionId):
        # Logic to listen for new responses for the given questionId
        while True:
            await asyncio.sleep(1)  # Adjust as necessary
            #check for new responses in your database
            yield ResponseType(id=1, question_id=questionId, section_id=1, value="Sample response")

class Mutation(ObjectType):
    create_assessment = CreateAssessment.Field()
    submit_response = SubmitResponse.Field()

class Subscription(ObjectType):
    response_subscription = ResponseSubscription.Field()


```