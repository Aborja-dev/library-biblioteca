---
  title: 'model.ts'
  description: 'component description'
  pubDate: 'August 2, 2024'
  author: 'Aborja-dev'
  ---
  
  
  
  # model.ts
  ## Code Explanation

### Data Models
- **UserModel**: Represents the structure of a user with properties like id, name, email, type, and typeId.
- **EventModel**: Represents the structure of an event with properties like id, name, url, startingDate, endingDate, timezone, description, organizerId, status, statusId, type, typeId, and optional properties like proposalsStartingDate, proposalsEndingDate, bannerUrl, and location.
- **TalkProposalModel**: Represents the structure of a talk proposal with properties like id, event, eventId, candidate, candidateId, status, statusId, title, abstract, estimatedDuration, attachmentsIds, streamed, uniqueCode, topicIds, and topics.
- **AttachmentOnProposal**: Represents the structure of an attachment on a proposal with properties like id, description, alt, url, file, proposal, and proposalId.
- **Topic**: Represents the structure of a topic with properties like id, name, description, and proposals.

### Interfaces and Types
- **ForEventRepositoryOperations**: Interface defining methods for event repository operations like insert, search, listAll, listBy, update, and delete.
- **ForInsertEvent**: Type defining the required properties for inserting an event.
- **ForUpdateEvent**: Type defining the properties that can be updated for an event.
- **ForProposalRepositoryOperations**: Interface defining methods for proposal repository operations like insert, getById, listAll, filterBy, update, and delete.
- **ForInsertProposal**: Type defining the required properties for inserting a proposal.
- **ForUpdateProposal**: Type defining the properties that can be updated for a proposal.
- **ForUserRepositoryOperations**: Interface defining methods for user repository operations like insert, getById, listBy, update, delete, search, and listAll.
- **ForInsertUser**: Type defining the required properties for inserting a user.
- **ForUpdateUser**: Type defining the properties that can be updated for a user.

### Enums
- **EventTypes**: Enum representing different types of events like PRESENCIAL, VIRTUAL, and HYBRID.
- **EventStatuses**: Enum representing different statuses of events like BORRADOR, EN_PROCESO, and FINALIZADO.
- **ProposalStatuses**: Enum representing different statuses of proposals like ENVIADA, EN_PROCESO, PRESELECCION, APROBADA, and RECHAZADA.
- **UserTypes**: Enum representing different types of users like ADMIN, USER, ORGANIZER, and CANDIDATE.
- **Topics**: Enum representing different topics like COMUNICACION, DESARROLLO, DISENO, and CIENCIA.

### Error Handling
- **CustomError**: Interface extending the Error interface to include custom properties like where, type, and info.
- **ModelError**: Custom error class that extends the Error class and implements the CustomError interface to provide detailed error information.

### Classes
- **EventRepo**: Class implementing the ForEventRepositoryOperations interface to perform CRUD operations related to events using a PrismaClient instance.
- **TalkProposalRepo**: Class implementing the ForProposalRepositoryOperations interface to perform CRUD operations related to talk proposals using a PrismaClient instance.
- **UserRepo**: Class implementing the ForUserRepositoryOperations interface to perform CRUD operations related to users using a PrismaClient instance.

### Usage Example
```typescript
// Creating a new event
const newEvent: ForInsertEvent = {
    name: 'Sample Event',
    url: 'https://sampleevent.com',
    startingDate: new Date(),
    endingDate: new Date(),
    timezone: 'UTC',
    typeId: EventTypes.VIRTUAL,
    proposalsStartingDate: new Date(),
    proposalsEndingDate: new Date(),
    description: 'Description of the event',
    bannerUrl: 'https://sampleevent.com/banner',
    location: 'Virtual',
    organizerId: ['organizer1', 'organizer2']
};

const eventRepo = new EventRepo(client);
eventRepo.insert(newEvent)
    .then(() => console.log('Event inserted successfully'))
    .catch((error) => console.error('Error inserting event:', error));
```

This code snippet demonstrates the creation of a new event using the `EventRepo` class and the `insert` method. The event details are provided in the `newEvent` object, and the insertion operation is performed asynchronously. If successful, a success message is logged; otherwise, an error message is displayed.
  
  ## Component Code
  ```jsx
  export const client: PrismaClient = new PrismaClient()

export type UserModel = {
    id: string;
    name: string;
    email: string;
    type: UserTypes;
    typeId: number;
  };

  export type EventModel = {
    id: string;
    name: string;
    url: string;
    startingDate: Date;
    endingDate: Date;
    timezone: string;
    description: string;
    organizerId: string[];
    status: EventStatuses;
    statusId: number;

    type: EventTypes;
    typeId: number;

    proposalsStartingDate?: Date;
    proposalsEndingDate?: Date;
    bannerUrl?: string;
    location?: string;
};

export type TalkProposalModel = {
  id: number;

  event?: {
    id: string;
    name: string;
  };
  eventId: string;
  
  candidate?: {
    id: string;
    name: string;
  };
  candidateId: string;

  status?: ProposalStatuses;
  statusId: number;

  title: string;
  abstract: string;
  estimatedDuration: number;
  attachmentsIds: number[];
  streamed: boolean;
  uniqueCode: string;
  topicIds: number[];
  topics: {
      name: string;
  }[];
};

export type AttachmentOnProposal = {
  id: number;
  description?: string;
  alt?: string;
  url: string;
  file: string;
  proposal: TalkProposalModel;
  proposalId: number;
};

// Tipo para Topic
export type Topic = {
  id: number;
  name: string;
  description?: string;
  proposals: TalkProposalModel[];
};

export interface ForEventRepositoryOperations {
    // Método para insertar un nuevo evento
    insert(input: ForInsertEvent): Promise<void>;

    // Método para buscar un evento por ID
    search(id: string): Promise<EventModel | null>;

    // Método para listar todos los eventos
    listAll(): Promise<EventModel[]>;

    // Método para listar eventos por estado
    listBy(status: EventStatuses): Promise<EventModel[]>;

    // Método para actualizar un evento
    update({ id, input }: { id: string, input: Partial<ForUpdateEvent> }): Promise<void>;

    // Método para eliminar un evento por ID
    delete(id: string): Promise<void>;
    
}

export type ForInsertEvent = Pick<EventModel, 'name' | 'url' | 'startingDate' | 'endingDate' | 'timezone' | 'typeId' | 'proposalsStartingDate' | 'proposalsEndingDate' | 'description' | 'bannerUrl' | 'location' | 'organizerId'>

export type ForUpdateEvent = Pick<EventModel, 'description' | 'bannerUrl' | 'location' | 'endingDate' | 'startingDate' | 'url' | 'statusId'>

export class EventRepo implements ForEventRepositoryOperations {
    constructor(
        private readonly dbConnection: PrismaClient
    ) { }

    async insert(input: ForInsertEvent): Promise<void> {
        const { name, url, startingDate, endingDate, timezone, typeId, proposalsStartingDate, proposalsEndingDate, description, bannerUrl, location, organizerId } = input 
        try {
            const event = await this.dbConnection.event.create({
                data: {
                    name,
                    url,
                    startingDate,
                    endingDate,
                    timezone,
                    type: {
                        connect: {
                            id: typeId
                        }
                    },
                    status: {
                        connect: {
                            id: 1
                        }
                    },
                    proposalsStartingDate,
                    proposalsEndingDate,
                    description,
                    bannerUrl,
                    location,
                    organizers: {
                        connect: organizerId.map((id) => ({ id }))
                    }
                }
            })
            console.log(event);
            
        } catch (error) {
            this.onError('insert', error.message, error)
        }
    }

    async search(id: string): Promise<EventModel | null> {
        try {
            const event = await this.dbConnection.event.findUnique({
                where: {
                    id
                },
                include: {
                    organizers: {
                        select: {
                            name: true,
                            email: true
                        }
                    }
                }
            })
            if (!event) return null
            return event
        } catch (error) {
            throw this.onError('getById', error.message, error)
        }
    }

    async listAll(): Promise<EventModel[]> {
        try {
            const events = await this.dbConnection.event.findMany()
            return events
        } catch (error) {
            throw this.onError('listAll', error.message, error)
        }
    }

    async listBy(status: EventStatuses): Promise<EventModel[]> {
        // TODO adaptarlo a otros criterios de busqueda
        try {
            const events = this.dbConnection.event.findMany({
                where: {
                    status: {
                        id: status + 1
                    }
                }
            })
            return events
        } catch (error) {
            throw this.onError('listBy', error.message, error)
        }
    }

    async update({ id, input }: { id: string, input: Partial<ForUpdateEvent> }): Promise<void> {
        try {
            await this.dbConnection.event.update({
                where: {
                    id
                },
                data: input
            })
        } catch (error) {
            throw this.onError('update', error.message, error)
        }
    }

    async delete(id: string): Promise<void> {
        try {
            await this.dbConnection.event.delete({
                where: {
                    id
                }
            })
        } catch (error) {
            throw this.onError('delete', error.message, error)
        }
    }
    private onError(where: string, message?: string, ...args: any) {
        throw new ModelError(`${this.constructor.name} ${where}`, message ?? 'Error', ...args)
    }
}

export interface ForProposalRepositoryOperations {
    insert(input: ForInsertProposal): Promise<void>;
    getById(id: number): Promise<TalkProposalModel | null>;
    listAll(): Promise<TalkProposalModel[]>;
    filterBy(candidateId: string): Promise<TalkProposalModel[]>;
    update(id: number, input: Partial<ForUpdateProposal>): Promise<void>;
    delete(id: number): Promise<void>;
  }

  export type ForInsertProposal = Pick<TalkProposalModel, 'title' | 'topicIds' | 'abstract' | 'estimatedDuration' | 'streamed' | 'eventId' | 'candidateId' | 'statusId' | 'attachmentsIds' >
export type ForUpdateProposal = Pick<TalkProposalModel,  'abstract' | 'estimatedDuration' | 'streamed' | 'statusId' | 'attachmentsIds' >

export class TalkProposalRepo implements ForProposalRepositoryOperations {

    constructor(
        private readonly dbConnection: PrismaClient
    ) {}
    async insert (input: ForInsertProposal): Promise<void> {
        const {title, topicIds, abstract, estimatedDuration, streamed, eventId, candidateId, statusId, attachmentsIds} = input
        try {
            await this.dbConnection.talkProposal.create({
                data: {
                    title,
                    topics: {
                        connect: topicIds.map(id => ({id}))
                    },
                    abstract,
                    estimatedDuration,
                    streamed,
                    event: {
                        connect: {id: eventId}
                    },
                    candidate: {
                        connect: {id: candidateId}
                    },
                    status: {
                        connect: {id: statusId}
                    },
                    attachments: {
                        connect: attachmentsIds.map(id => ({id}))
                    }
                }
            })
        } catch (error: any) {
            throw this.onError('insert', error.message, error)
        }
        
    }

    async getById (id: number): Promise<TalkProposalModel | null> {
        try {
            const proposal = await this.dbConnection.talkProposal.findUnique({
                where: {
                    id
                },
                include: {
                    topics: {
                        select: {
                            name: true
                        }
                    },
                    candidate: {
                        select: {
                            id: true,
                            name: true
                        }
                    },
                    event: {
                        select: {
                            id: true,
                            name: true
                        }
                    }
                }
            })
            return proposal
        } catch (error: any) {
            throw this.onError('getById', error.message, error)
        }
    }

    async listAll (): Promise<TalkProposalModel[]> {
        try {
            const proposals = await this.dbConnection.talkProposal.findMany({
                include: {
                    topics: {
                        select: {
                            name: true
                        }
                    },
                    candidate: {
                        select: {
                            id: true,
                            name: true
                        }
                    },
                    event: {
                        select: {
                            id: true,
                            name: true
                        }
                    }
                }})
            return proposals
        } catch (error: any) {
            throw this.onError('listAll', error.message, error)
        }
    }

    async filterBy (candidateId: string): Promise<TalkProposalModel[]> {
        try {
            const proposals = await this.dbConnection.talkProposal.findMany({
                where: {
                    candidateId
                },
                include: {
                    topics: {
                        select: {
                            name: true
                        }
                    },
                    candidate: {
                        select: {
                            id: true,
                            name: true
                        }
                    },
                    event: {
                        select: {
                            id: true,
                            name: true
                        }
                    }
                }})
            return proposals
        } catch (error: any) {
            throw this.onError('filterBy', error.message, error)
        }       
    }

    async update (id: number, input: Partial<ForUpdateProposal>): Promise<void> {
        try {
            await this.dbConnection.talkProposal.update({
                where: {
                    id
                },
                data: input
            })
        } catch (error: any) {
            throw this.onError('update', error.message, error)
        }
    }

    async delete (id: number): Promise<void> {
        try {
            await this.dbConnection.talkProposal.delete({
                where: {
                    id
                }
            })
        } catch (error: any) {
            throw this.onError('delete', error.message, error)
        }
    }
    private onError(where: string, message?: string, ...args: any) {
        throw new ModelError( `${this.constructor.name} ${where}`, message ?? 'Error' ,...args)
    }

  }

  export interface ForUserRepositoryOperations {
    // Método para insertar un nuevo usuario
    insert(input: ForInsertUser): Promise<void>;

    // Método para obtener un usuario por ID
    getById(id: string): Promise<UserModel | null>;

    // Método para listar usuarios por tipo
    listBy(type: UserTypes): Promise<UserModel[]>;

    // Método para actualizar un usuario
    update({id, input}: {id: string, input: Partial<ForUpdateUser>}): Promise<void>;

    // Método para eliminar un usuario por ID
    delete(id: string): Promise<void>;

    // Método para buscar un usuario por ID
    search(id: string): Promise<UserModel | null>;

    // Método para listar todos los usuarios
    listAll(): Promise<UserModel[]>;
}

export class UserRepo implements ForUserRepositoryOperations {
    constructor (
        private readonly dbConnection: PrismaClient
    ) {}

    async insert (input: ForInsertUser): Promise<void>{
        const {email, name, typeId} = input
        try {
            await this.dbConnection.user.create({
                data: {
                    email,
                    name,
                    type: {
                        connect: {
                            id: typeId
                        }
                    }
                }
            })
        } catch (error) {
            this.onError('insert')
        }
        
    }
    async getById (id: string): Promise<UserModel | null> {
        try {
            const user = await this.dbConnection.user.findUnique({
                where: {
                    id
                }
            })
            if (!user) return null
            return user
        } catch (error) {
            throw this.onError('getById')
        }
    }
    async listBy (type: UserTypes): Promise<UserModel[]> {
        try {
            const users = await this.dbConnection.user.findMany({
                where: {
                    type: {
                        id: type +1
                    }
                }
            })
            return users
        } catch (error) {
            throw this.onError('listBy')
        }
    }
    async update ({id, input}: {id: string, input: Partial<ForUpdateUser>}): Promise<void> {
        try {
            await this.dbConnection.user.update({
                where: {
                    id
                },
                data: input
            })
        } catch (error) {
            throw this.onError('update')
        }
    }
    async delete (id: string): Promise<void> {
        try {
            await this.dbConnection.user.delete({
                where: {
                    id
                }
            })
        } catch (error) {
            throw this.onError('delete')
        }
    }
    async search (id: string): Promise<UserModel | null> {
        try {
            const user = await this.dbConnection.user.findUnique({
                where: {
                    id
                }
            })
            if (!user) return null
            return user
        } catch (error) {
            throw this.onError('search')
        }
    }
    async listAll (): Promise<UserModel[]> {
        try {
            const users = await this.dbConnection.user.findMany()
            return users
        } catch (error) {
            throw this.onError('listAll')
        }
    }
    private onError (where: string, message?: string, ...args: any) {
        throw new ModelError( `${this.constructor.name} ${where}`, message ?? 'Error' ,...args)
    }
}

export type ForInsertUser = Pick<UserModel, 'name' | 'email' | 'typeId'>
export type ForUpdateUser = Pick<UserModel, 'name' | 'typeId'>

export enum EventTypes {
    PRESENCIAL, // 0
    VIRTUAL,    // 1
    HYBRID      // 2
  }
  
  export enum EventStatuses {
    BORRADOR,    // 0
    EN_PROCESO,  // 1
    FINALIZADO   // 2
  }
  
  export enum ProposalStatuses {
    ENVIADA,      // 0
    EN_PROCESO,   // 1
    PRESELECCION, // 2
    APROBADA,     // 3
    RECHAZADA     // 4
  }
  
  export enum UserTypes {
    ADMIN,     // 0
    USER,      // 1
    ORGANIZER, // 2
    CANDIDATE  // 3
  }
  
  export enum Topics {
    COMUNICACION, // 0
    DESARROLLO,   // 1
    DISENO,       // 2
    CIENCIA       // 3
  }
  export interface CustomError extends Error {
    where: string
    type: string
    info: any
}

export class ModelError extends Error implements CustomError {
    where: string
    type: string
    info: any
    constructor (where: string, message: string, ...args: any) {
        super(message ?? where);
        this.where = where
        this.type = 'ModelError'
        this.info = args
    }
}
  ```
  