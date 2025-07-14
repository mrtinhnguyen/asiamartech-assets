PHẦN 1: ĐIỀU CHỈNH BẢNG HIỆN CÓ
-- =====================================================
-- 1. BẢNG User - BỔ SUNG THÊM TRƯỜNG
-- =====================================================

ALTER TABLE "User" 
ADD COLUMN IF NOT EXISTS role VARCHAR(20) DEFAULT 'staff',
ADD COLUMN IF NOT EXISTS permissions JSONB,
ADD COLUMN IF NOT EXISTS is_active BOOLEAN DEFAULT TRUE,
ADD COLUMN IF NOT EXISTS last_login TIMESTAMP,
ADD COLUMN IF NOT EXISTS created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
ADD COLUMN IF NOT EXISTS updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP;

-- Thêm comment cho các cột mới
COMMENT ON COLUMN "User".role IS 'admin, staff, volunteer';
COMMENT ON COLUMN "User".permissions IS 'Danh sách quyền hạn';
COMMENT ON COLUMN "User".is_active IS 'Trạng thái hoạt động';
COMMENT ON COLUMN "User".last_login IS 'Lần đăng nhập cuối';

-- =====================================================
-- 2. BẢNG detailTicket - ĐIỀU CHỈNH VÀ BỔ SUNG
-- =====================================================

-- Thêm các cột mới
ALTER TABLE detailTicket 
ADD COLUMN IF NOT EXISTS ticket_type VARCHAR(20) DEFAULT 'regular',
ADD COLUMN IF NOT EXISTS status VARCHAR(20) DEFAULT 'valid',
ADD COLUMN IF NOT EXISTS checked_in_by VARCHAR(100),
ADD COLUMN IF NOT EXISTS location_lat DECIMAL(10,8),
ADD COLUMN IF NOT EXISTS location_lng DECIMAL(11,8),
ADD COLUMN IF NOT EXISTS device_info JSONB,
ADD COLUMN IF NOT EXISTS notes TEXT,
ADD COLUMN IF NOT EXISTS sync_status VARCHAR(20) DEFAULT 'synced',
ADD COLUMN IF NOT EXISTS session_id VARCHAR(50),
ADD COLUMN IF NOT EXISTS created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
ADD COLUMN IF NOT EXISTS updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP;

-- Thêm comment cho các cột mới
COMMENT ON COLUMN detailTicket.ticket_type IS 'vip, regular, student, complimentary';
COMMENT ON COLUMN detailTicket.status IS 'valid, checked_in, expired, cancelled';
COMMENT ON COLUMN detailTicket.checked_in_by IS 'ID người thực hiện check-in';
COMMENT ON COLUMN detailTicket.location_lat IS 'Vĩ độ khi check-in';
COMMENT ON COLUMN detailTicket.location_lng IS 'Kinh độ khi check-in';
COMMENT ON COLUMN detailTicket.device_info IS 'Thông tin thiết bị check-in';
COMMENT ON COLUMN detailTicket.sync_status IS 'synced, pending, failed';

-- Đổi tên cột nếu chưa đổi
DO $$ 
BEGIN
    IF EXISTS (SELECT 1 FROM information_schema.columns WHERE table_name = 'detailticket' AND column_name = 'checkIn') THEN
        ALTER TABLE detailTicket RENAME COLUMN "checkIn" TO is_checked_in;
    END IF;
    
    IF EXISTS (SELECT 1 FROM information_schema.columns WHERE table_name = 'detailticket' AND column_name = 'timeCheckIn') THEN
        ALTER TABLE detailTicket RENAME COLUMN "timeCheckIn" TO checked_in_at;
    END IF;
END $$;

-- =====================================================
-- 3. BẢNG userCheckin - BỔ SUNG THÊM TRƯỜNG
-- =====================================================

ALTER TABLE userCheckin 
ADD COLUMN IF NOT EXISTS role VARCHAR(20) DEFAULT 'staff',
ADD COLUMN IF NOT EXISTS permissions JSONB,
ADD COLUMN IF NOT EXISTS is_active BOOLEAN DEFAULT TRUE,
ADD COLUMN IF NOT EXISTS last_login TIMESTAMP,
ADD COLUMN IF NOT EXISTS login_attempts INTEGER DEFAULT 0,
ADD COLUMN IF NOT EXISTS locked_until TIMESTAMP,
ADD COLUMN IF NOT EXISTS created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
ADD COLUMN IF NOT EXISTS updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP;

-- Thêm comment cho các cột mới
COMMENT ON COLUMN userCheckin.role IS 'admin, staff, volunteer';
COMMENT ON COLUMN userCheckin.permissions IS 'Quyền hạn cụ thể cho event này';
COMMENT ON COLUMN userCheckin.login_attempts IS 'Số lần đăng nhập thất bại';
COMMENT ON COLUMN userCheckin.locked_until IS 'Thời gian khóa tài khoản';

-- =====================================================
-- 4. BẢNG client_app_id - BỔ SUNG THÊM TRƯỜNG
-- =====================================================

ALTER TABLE client_app_id 
ADD COLUMN IF NOT EXISTS app_version VARCHAR(20),
ADD COLUMN IF NOT EXISTS device_model VARCHAR(100),
ADD COLUMN IF NOT EXISTS os_version VARCHAR(20),
ADD COLUMN IF NOT EXISTS fcm_token VARCHAR(255),
ADD COLUMN IF NOT EXISTS last_active TIMESTAMP,
ADD COLUMN IF NOT EXISTS is_active BOOLEAN DEFAULT TRUE,
ADD COLUMN IF NOT EXISTS created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
ADD COLUMN IF NOT EXISTS updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP;

-- Thêm comment cho các cột mới
COMMENT ON COLUMN client_app_id.app_version IS 'Phiên bản app';
COMMENT ON COLUMN client_app_id.device_model IS 'Model thiết bị';
COMMENT ON COLUMN client_app_id.os_version IS 'Phiên bản hệ điều hành';
COMMENT ON COLUMN client_app_id.fcm_token IS 'Firebase Cloud Messaging token';
COMMENT ON COLUMN client_app_id.last_active IS 'Lần hoạt động cuối';

-- =====================================================
-- 5. BẢNG Ticket - BỔ SUNG THÊM TRƯỜNG (NẾU CẦN)
-- =====================================================

-- Thêm các cột cần thiết cho check-in app
ALTER TABLE "Ticket" 
ADD COLUMN IF NOT EXISTS checkin_enabled BOOLEAN DEFAULT TRUE,
ADD COLUMN IF NOT EXISTS checkin_settings JSONB,
ADD COLUMN IF NOT EXISTS sync_status VARCHAR(20) DEFAULT 'synced';

-- Thêm comment cho các cột mới
COMMENT ON COLUMN "Ticket".checkin_enabled IS 'Cho phép check-in loại vé này';
COMMENT ON COLUMN "Ticket".checkin_settings IS 'Cài đặt check-in cho loại vé';
COMMENT ON COLUMN "Ticket".sync_status IS 'Trạng thái đồng bộ với check-in app';

-- =====================================================
-- 6. BẢNG Attendee - BỔ SUNG THÊM TRƯỜNG (NẾU CẦN)
-- =====================================================

-- Thêm các cột cần thiết cho check-in app
ALTER TABLE "Attendee" 
ADD COLUMN IF NOT EXISTS checkin_device_info JSONB,
ADD COLUMN IF NOT EXISTS checkin_location_lat DECIMAL(10,8),
ADD COLUMN IF NOT EXISTS checkin_location_lng DECIMAL(11,8),
ADD COLUMN IF NOT EXISTS checkin_notes TEXT,
ADD COLUMN IF NOT EXISTS checkin_session_id VARCHAR(50),
ADD COLUMN IF NOT EXISTS sync_status VARCHAR(20) DEFAULT 'synced';

-- Thêm comment cho các cột mới
COMMENT ON COLUMN "Attendee".checkin_device_info IS 'Thông tin thiết bị check-in';
COMMENT ON COLUMN "Attendee".checkin_location_lat IS 'Vĩ độ khi check-in';
COMMENT ON COLUMN "Attendee".checkin_location_lng IS 'Kinh độ khi check-in';
COMMENT ON COLUMN "Attendee".checkin_notes IS 'Ghi chú khi check-in';
COMMENT ON COLUMN "Attendee".checkin_session_id IS 'ID phiên check-in';
COMMENT ON COLUMN "Attendee".sync_status IS 'Trạng thái đồng bộ với check-in app';

PHẦN 2: TẠO BẢNG MỚI
-- =====================================================
-- 7. BẢNG CheckInSession - QUẢN LÝ PHIÊN CHECK-IN
-- =====================================================

CREATE TABLE IF NOT EXISTS "CheckInSession" (
    id VARCHAR(50) PRIMARY KEY,
    "eventId" VARCHAR(50) NOT NULL,
    user_id VARCHAR(50) NOT NULL,
    session_name VARCHAR(255),
    start_time TIMESTAMP,
    end_time TIMESTAMP,
    location VARCHAR(255),
    device_info JSONB,
    status VARCHAR(20) DEFAULT 'active',
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Thêm comment cho bảng và cột
COMMENT ON TABLE "CheckInSession" IS 'Quản lý phiên check-in';
COMMENT ON COLUMN "CheckInSession".status IS 'active, paused, ended';
COMMENT ON COLUMN "CheckInSession".device_info IS 'Thông tin thiết bị';

-- =====================================================
-- 8. BẢNG CheckInLog - LOG CHI TIẾT CHECK-IN
-- =====================================================

CREATE TABLE IF NOT EXISTS "CheckInLog" (
    id VARCHAR(50) PRIMARY KEY,
    session_id VARCHAR(50),
    attendee_id VARCHAR(50) NOT NULL,
    ticket_id VARCHAR(50) NOT NULL,
    checked_in_by VARCHAR(50) NOT NULL,
    check_in_time TIMESTAMP NOT NULL,
    location_lat DECIMAL(10,8),
    location_lng DECIMAL(11,8),
    device_info JSONB,
    notes TEXT,
    sync_status VARCHAR(20) DEFAULT 'synced',
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Thêm comment cho bảng và cột
COMMENT ON TABLE "CheckInLog" IS 'Log chi tiết check-in';
COMMENT ON COLUMN "CheckInLog".sync_status IS 'synced, pending, failed';

-- =====================================================
-- 9. BẢNG SyncQueue - QUẢN LÝ ĐỒNG BỘ OFFLINE
-- =====================================================

CREATE TABLE IF NOT EXISTS "SyncQueue" (
    id VARCHAR(50) PRIMARY KEY,
    user_id VARCHAR(50) NOT NULL,
    "eventId" VARCHAR(50) NOT NULL,
    action_type VARCHAR(50) NOT NULL,
    data JSONB NOT NULL,
    status VARCHAR(20) DEFAULT 'pending',
    retry_count INTEGER DEFAULT 0,
    max_retries INTEGER DEFAULT 3,
    error_message TEXT,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Thêm comment cho bảng và cột
COMMENT ON TABLE "SyncQueue" IS 'Quản lý đồng bộ offline';
COMMENT ON COLUMN "SyncQueue".action_type IS 'checkin, update, delete';
COMMENT ON COLUMN "SyncQueue".status IS 'pending, processing, completed, failed';

-- =====================================================
-- 10. BẢNG Analytics - THỐNG KÊ VÀ PHÂN TÍCH
-- =====================================================

CREATE TABLE IF NOT EXISTS "Analytics" (
    id VARCHAR(50) PRIMARY KEY,
    "eventId" VARCHAR(50) NOT NULL,
    metric_type VARCHAR(50) NOT NULL,
    metric_name VARCHAR(100) NOT NULL,
    metric_value DECIMAL(10,4),
    metadata JSONB,
    timestamp TIMESTAMP NOT NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Thêm comment cho bảng và cột
COMMENT ON TABLE "Analytics" IS 'Thống kê và phân tích';
COMMENT ON COLUMN "Analytics".metric_type IS 'checkin_rate, performance, error';

PHẦN 3: THIẾT LẬP QUAN HỆ (FOREIGN KEYS)
-- =====================================================
-- THIẾT LẬP FOREIGN KEYS
-- =====================================================

-- Foreign Keys cho bảng detailTicket
DO $$ 
BEGIN
    -- Kiểm tra và thêm FK cho attendeeId
    IF NOT EXISTS (
        SELECT 1 FROM information_schema.table_constraints 
        WHERE constraint_name = 'fk_detailticket_attendee'
    ) THEN
        ALTER TABLE detailTicket 
        ADD CONSTRAINT fk_detailticket_attendee 
        FOREIGN KEY ("attendeeId") REFERENCES "Attendee"(id);
    END IF;
    
    -- Kiểm tra và thêm FK cho eventId
    IF NOT EXISTS (
        SELECT 1 FROM information_schema.table_constraints 
        WHERE constraint_name = 'fk_detailticket_event'
    ) THEN
        ALTER TABLE detailTicket 
        ADD CONSTRAINT fk_detailticket_event 
        FOREIGN KEY ("eventId") REFERENCES "Event"(id);
    END IF;
    
    -- Kiểm tra và thêm FK cho ticketId
    IF NOT EXISTS (
        SELECT 1 FROM information_schema.table_constraints 
        WHERE constraint_name = 'fk_detailticket_ticket'
    ) THEN
        ALTER TABLE detailTicket 
        ADD CONSTRAINT fk_detailticket_ticket 
        FOREIGN KEY ("ticketId") REFERENCES "Ticket"(id);
    END IF;
END $$;

-- Foreign Keys cho bảng userCheckin
DO $$ 
BEGIN
    -- Kiểm tra và thêm FK cho eventId
    IF NOT EXISTS (
        SELECT 1 FROM information_schema.table_constraints 
        WHERE constraint_name = 'fk_usercheckin_event'
    ) THEN
        ALTER TABLE userCheckin 
        ADD CONSTRAINT fk_usercheckin_event 
        FOREIGN KEY ("eventId") REFERENCES "Event"(id);
    END IF;
    
    -- Kiểm tra và thêm FK cho userId
    IF NOT EXISTS (
        SELECT 1 FROM information_schema.table_constraints 
        WHERE constraint_name = 'fk_usercheckin_user'
    ) THEN
        ALTER TABLE userCheckin 
        ADD CONSTRAINT fk_usercheckin_user 
        FOREIGN KEY ("userId") REFERENCES "User"(id);
    END IF;
END $$;

-- Foreign Keys cho bảng CheckInSession
DO $$ 
BEGIN
    IF NOT EXISTS (
        SELECT 1 FROM information_schema.table_constraints 
        WHERE constraint_name = 'fk_checkinsession_event'
    ) THEN
        ALTER TABLE "CheckInSession" 
        ADD CONSTRAINT fk_checkinsession_event 
        FOREIGN KEY ("eventId") REFERENCES "Event"(id);
    END IF;
    
    IF NOT EXISTS (
        SELECT 1 FROM information_schema.table_constraints 
        WHERE constraint_name = 'fk_checkinsession_user'
    ) THEN
        ALTER TABLE "CheckInSession" 
        ADD CONSTRAINT fk_checkinsession_user 
        FOREIGN KEY (user_id) REFERENCES "User"(id);
    END IF;
END $$;

-- Foreign Keys cho bảng CheckInLog
DO $$ 
BEGIN
    IF NOT EXISTS (
        SELECT 1 FROM information_schema.table_constraints 
        WHERE constraint_name = 'fk_checkinlog_session'
    ) THEN
        ALTER TABLE "CheckInLog" 
        ADD CONSTRAINT fk_checkinlog_session 
        FOREIGN KEY (session_id) REFERENCES "CheckInSession"(id);
    END IF;
    
    IF NOT EXISTS (
        SELECT 1 FROM information_schema.table_constraints 
        WHERE constraint_name = 'fk_checkinlog_attendee'
    ) THEN
        ALTER TABLE "CheckInLog" 
        ADD CONSTRAINT fk_checkinlog_attendee 
        FOREIGN KEY (attendee_id) REFERENCES "Attendee"(id);
    END IF;
    
    IF NOT EXISTS (
        SELECT 1 FROM information_schema.table_constraints 
        WHERE constraint_name = 'fk_checkinlog_ticket'
    ) THEN
        ALTER TABLE "CheckInLog" 
        ADD CONSTRAINT fk_checkinlog_ticket 
        FOREIGN KEY (ticket_id) REFERENCES "Ticket"(id);
    END IF;
    
    IF NOT EXISTS (
        SELECT 1 FROM information_schema.table_constraints 
        WHERE constraint_name = 'fk_checkinlog_user'
    ) THEN
        ALTER TABLE "CheckInLog" 
        ADD CONSTRAINT fk_checkinlog_user 
        FOREIGN KEY (checked_in_by) REFERENCES "User"(id);
    END IF;
END $$;

-- Foreign Keys cho bảng SyncQueue
DO $$ 
BEGIN
    IF NOT EXISTS (
        SELECT 1 FROM information_schema.table_constraints 
        WHERE constraint_name = 'fk_syncqueue_user'
    ) THEN
        ALTER TABLE "SyncQueue" 
        ADD CONSTRAINT fk_syncqueue_user 
        FOREIGN KEY (user_id) REFERENCES "User"(id);
    END IF;
    
    IF NOT EXISTS (
        SELECT 1 FROM information_schema.table_constraints 
        WHERE constraint_name = 'fk_syncqueue_event'
    ) THEN
        ALTER TABLE "SyncQueue" 
        ADD CONSTRAINT fk_syncqueue_event 
        FOREIGN KEY ("eventId") REFERENCES "Event"(id);
    END IF;
END $$;

-- Foreign Keys cho bảng Analytics
DO $$ 
BEGIN
    IF NOT EXISTS (
        SELECT 1 FROM information_schema.table_constraints 
        WHERE constraint_name = 'fk_analytics_event'
    ) THEN
        ALTER TABLE "Analytics" 
        ADD CONSTRAINT fk_analytics_event 
        FOREIGN KEY ("eventId") REFERENCES "Event"(id);
    END IF;
END $$;

📊 PHẦN 4: TẠO INDEXES
-- =====================================================
-- TẠO INDEXES ĐỂ TỐI ƯU HIỆU SUẤT
-- =====================================================

-- Indexes cho bảng detailTicket
CREATE INDEX IF NOT EXISTS idx_detailticket_eventid ON detailTicket("eventId");
CREATE INDEX IF NOT EXISTS idx_detailticket_code ON detailTicket("codeTicket");
CREATE INDEX IF NOT EXISTS idx_detailticket_checkin ON detailTicket(is_checked_in);
CREATE INDEX IF NOT EXISTS idx_detailticket_sync ON detailTicket(sync_status);
CREATE INDEX IF NOT EXISTS idx_detailticket_attendee ON detailTicket("attendeeId");

-- Indexes cho bảng userCheckin
CREATE INDEX IF NOT EXISTS idx_usercheckin_email ON userCheckin(email);
CREATE INDEX IF NOT EXISTS idx_usercheckin_code ON userCheckin("codeLogin");
CREATE INDEX IF NOT EXISTS idx_usercheckin_event ON userCheckin("eventId");
CREATE INDEX IF NOT EXISTS idx_usercheckin_active ON userCheckin(is_active);

-- Indexes cho bảng CheckInLog
CREATE INDEX IF NOT EXISTS idx_checkinlog_time ON "CheckInLog"(check_in_time);
CREATE INDEX IF NOT EXISTS idx_checkinlog_session ON "CheckInLog"(session_id);
CREATE INDEX IF NOT EXISTS idx_checkinlog_sync ON "CheckInLog"(sync_status);
CREATE INDEX IF NOT EXISTS idx_checkinlog_attendee ON "CheckInLog"(attendee_id);
CREATE INDEX IF NOT EXISTS idx_checkinlog_ticket ON "CheckInLog"(ticket_id);

-- Indexes cho bảng SyncQueue
CREATE INDEX IF NOT EXISTS idx_syncqueue_status ON "SyncQueue"(status);
CREATE INDEX IF NOT EXISTS idx_syncqueue_user ON "SyncQueue"(user_id);
CREATE INDEX IF NOT EXISTS idx_syncqueue_event ON "SyncQueue"("eventId");
CREATE INDEX IF NOT EXISTS idx_syncqueue_created ON "SyncQueue"(created_at);

-- Indexes cho bảng Analytics
CREATE INDEX IF NOT EXISTS idx_analytics_event ON "Analytics"("eventId");
CREATE INDEX IF NOT EXISTS idx_analytics_type ON "Analytics"(metric_type);
CREATE INDEX IF NOT EXISTS idx_analytics_timestamp ON "Analytics"(timestamp);

-- Indexes cho bảng CheckInSession
CREATE INDEX IF NOT EXISTS idx_checkinsession_event ON "CheckInSession"("eventId");
CREATE INDEX IF NOT EXISTS idx_checkinsession_user ON "CheckInSession"(user_id);
CREATE INDEX IF NOT EXISTS idx_checkinsession_status ON "CheckInSession"(status);

-- Indexes cho bảng Attendee (bổ sung)
CREATE INDEX IF NOT EXISTS idx_attendee_event ON "Attendee"("eventId");
CREATE INDEX IF NOT EXISTS idx_attendee_user ON "Attendee"("userId");
CREATE INDEX IF NOT EXISTS idx_attendee_checkin ON "Attendee"("checkIn");
CREATE INDEX IF NOT EXISTS idx_attendee_sync ON "Attendee"(sync_status);

-- Indexes cho bảng Ticket (bổ sung)
CREATE INDEX IF NOT EXISTS idx_ticket_event ON "Ticket"("eventId");
CREATE INDEX IF NOT EXISTS idx_ticket_active ON "Ticket"(active);
CREATE INDEX IF NOT EXISTS idx_ticket_checkin ON "Ticket"(checkin_enabled);

PHẦN 5: TẠO TRIGGERS VÀ FUNCTIONS
-- =====================================================
-- TẠO TRIGGERS ĐỂ TỰ ĐỘNG CẬP NHẬT updated_at
-- =====================================================

-- Function để cập nhật updated_at
CREATE OR REPLACE FUNCTION update_updated_at_column()
RETURNS TRIGGER AS $$
BEGIN
    NEW.updated_at = CURRENT_TIMESTAMP;
    RETURN NEW;
END;
$$ language 'plpgsql';

-- Triggers cho các bảng
DO $$ 
BEGIN
    -- Trigger cho bảng User
    IF NOT EXISTS (SELECT 1 FROM pg_trigger WHERE tgname = 'update_user_updated_at') THEN
        CREATE TRIGGER update_user_updated_at 
        BEFORE UPDATE ON "User" 
        FOR EACH ROW EXECUTE FUNCTION update_updated_at_column();
    END IF;
    
    -- Trigger cho bảng detailTicket
    IF NOT EXISTS (SELECT 1 FROM pg_trigger WHERE tgname = 'update_detailticket_updated_at') THEN
        CREATE TRIGGER update_detailticket_updated_at 
        BEFORE UPDATE ON detailTicket 
        FOR EACH ROW EXECUTE FUNCTION update_updated_at_column();
    END IF;
    
    -- Trigger cho bảng userCheckin
    IF NOT EXISTS (SELECT 1 FROM pg_trigger WHERE tgname = 'update_usercheckin_updated_at') THEN
        CREATE TRIGGER update_usercheckin_updated_at 
        BEFORE UPDATE ON userCheckin 
        FOR EACH ROW EXECUTE FUNCTION update_updated_at_column();
    END IF;
    
    -- Trigger cho bảng client_app_id
    IF NOT EXISTS (SELECT 1 FROM pg_trigger WHERE tgname = 'update_client_app_id_updated_at') THEN
        CREATE TRIGGER update_client_app_id_updated_at 
        BEFORE UPDATE ON client_app_id 
        FOR EACH ROW EXECUTE FUNCTION update_updated_at_column();
    END IF;
    
    -- Trigger cho bảng CheckInSession
    IF NOT EXISTS (SELECT 1 FROM pg_trigger WHERE tgname = 'update_checkinsession_updated_at') THEN
        CREATE TRIGGER update_checkinsession_updated_at 
        BEFORE UPDATE ON "CheckInSession" 
        FOR EACH ROW EXECUTE FUNCTION update_updated_at_column();
    END IF;
    
    -- Trigger cho bảng SyncQueue
    IF NOT EXISTS (SELECT 1 FROM pg_trigger WHERE tgname = 'update_syncqueue_updated_at') THEN
        CREATE TRIGGER update_syncqueue_updated_at 
        BEFORE UPDATE ON "SyncQueue" 
        FOR EACH ROW EXECUTE FUNCTION update_updated_at_column();
    END IF;
END $$;

-- =====================================================
-- TRIGGER TỰ ĐỘNG CẬP NHẬT CHECK-IN STATUS
-- =====================================================

-- Function để cập nhật trạng thái check-in
CREATE OR REPLACE FUNCTION update_checkin_status()
RETURNS TRIGGER AS $$
BEGIN
    -- Cập nhật trạng thái check-in trong bảng Attendee
    IF NEW."checkIn" = TRUE AND OLD."checkIn" = FALSE THEN
        UPDATE "Attendee" 
        SET 
            "checkInDate" = CURRENT_TIMESTAMP,
            checkin_device_info = NEW.device_info,
            checkin_location_lat = NEW.location_lat,
            checkin_location_lng = NEW.location_lng,
            checkin_notes = NEW.notes,
            checkin_session_id = NEW.session_id,
            sync_status = 'synced',
            "updatedAt" = CURRENT_TIMESTAMP
        WHERE id = NEW."attendeeId";
    END IF;
    
    RETURN NEW;
END;
$$ language 'plpgsql';

-- Trigger cho bảng detailTicket
DO $$ 
BEGIN
    IF NOT EXISTS (SELECT 1 FROM pg_trigger WHERE tgname = 'update_checkin_status_trigger') THEN
        CREATE TRIGGER update_checkin_status_trigger 
        AFTER UPDATE ON detailTicket 
        FOR EACH ROW EXECUTE FUNCTION update_checkin_status();
    END IF;
END $$;


PHẦN 6: TẠO VIEWS HỮU ÍCH
-- =====================================================
-- TẠO VIEWS ĐỂ DỄ DÀNG TRUY VẤN
-- =====================================================

-- View thống kê check-in theo sự kiện
CREATE OR REPLACE VIEW "EventCheckInStats" AS
SELECT 
    e.id as event_id,
    e.name as event_name,
    e."startAt",
    e."endAt",
    e."capacityMax",
    COUNT(a.id) as total_attendees,
    COUNT(CASE WHEN a."checkIn" = TRUE THEN 1 END) as checked_in_count,
    COUNT(CASE WHEN a."checkIn" = FALSE THEN 1 END) as pending_count,
    ROUND(
        (COUNT(CASE WHEN a."checkIn" = TRUE THEN 1 END)::DECIMAL / 
         NULLIF(COUNT(a.id), 0)) * 100, 2
    ) as checkin_rate
FROM "Event" e
LEFT JOIN "Attendee" a ON e.id = a."eventId"
GROUP BY e.id, e.name, e."startAt", e."endAt", e."capacityMax";

-- View danh sách user check-in theo sự kiện
CREATE OR REPLACE VIEW "EventCheckInUsers" AS
SELECT 
    e.id as event_id,
    e.name as event_name,
    uc.id as user_checkin_id,
    uc.email,
    uc.role,
    uc.is_active,
    u.name as user_name,
    uc.last_login
FROM "Event" e
INNER JOIN userCheckin uc ON e.id = uc."eventId"
LEFT JOIN "User" u ON uc."userId" = u.id
WHERE uc.is_active = TRUE;

-- View log check-in chi tiết
CREATE OR REPLACE VIEW "CheckInLogDetail" AS
SELECT 
    cl.id,
    cl.check_in_time,
    e.name as event_name,
    a.id as attendee_id,
    u.name as attendee_name,
    u.email as attendee_email,
    t.name as ticket_name,
    checked_user.email as checked_by_email,
    cl.location_lat,
    cl.location_lng,
    cl.notes,
    cs.session_name
FROM "CheckInLog" cl
INNER JOIN "Attendee" a ON cl.attendee_id = a.id
INNER JOIN "Event" e ON a."eventId" = e.id
INNER JOIN "User" u ON a."userId" = u.id
INNER JOIN "Ticket" t ON cl.ticket_id = t.id
INNER JOIN "User" checked_user ON cl.checked_in_by = checked_user.id
LEFT JOIN "CheckInSession" cs ON cl.session_id = cs.id
ORDER BY cl.check_in_time DESC;

-- View thống kê theo loại vé
CREATE OR REPLACE VIEW "TicketTypeStats" AS
SELECT 
    t.id as ticket_id,
    t.name as ticket_name,
    e.name as event_name,
    t.price,
    t.currency,
    t.quantity as max_quantity,
    t.sold as sold_quantity,
    COUNT(a.id) as total_attendees,
    COUNT(CASE WHEN a."checkIn" = TRUE THEN 1 END) as checked_in_count,
    ROUND(
        (COUNT(CASE WHEN a."checkIn" = TRUE THEN 1 END)::DECIMAL / 
         NULLIF(COUNT(a.id), 0)) * 100, 2
    ) as checkin_rate
FROM "Ticket" t
INNER JOIN "Event" e ON t."eventId" = e.id
LEFT JOIN "Attendee" a ON t.id = a."eventId" AND a."role" = 'attendee'
GROUP BY t.id, t.name, e.name, t.price, t.currency, t.quantity, t.sold;


✅ PHẦN 7: KIỂM TRA VÀ XÁC NHẬN
-- =====================================================
-- KIỂM TRA CẤU TRÚC BẢNG ĐÃ TẠO
-- =====================================================

-- Kiểm tra các bảng đã được tạo
SELECT table_name 
FROM information_schema.tables 
WHERE table_schema = 'public' 
AND table_name IN (
    'User', 'detailTicket', 'userCheckin', 'client_app_id',
    'Ticket', 'Attendee', 'CheckInSession', 'CheckInLog', 
    'SyncQueue', 'Analytics'
)
ORDER BY table_name;

-- Kiểm tra các cột mới đã được thêm
SELECT 
    table_name,
    column_name,
    data_type,
    is_nullable
FROM information_schema.columns 
WHERE table_schema = 'public'
AND table_name IN ('User', 'detailTicket', 'userCheckin', 'client_app_id', 'Ticket', 'Attendee')
AND column_name IN (
    'role', 'permissions', 'is_active', 'last_login', 'created_at', 'updated_at',
    'ticket_type', 'status', 'checked_in_by', 'location_lat', 'location_lng',
    'device_info', 'notes', 'sync_status', 'session_id',
    'checkin_enabled', 'checkin_settings', 'checkin_device_info', 'checkin_location_lat',
    'checkin_location_lng', 'checkin_notes', 'checkin_session_id'
)
ORDER BY table_name, column_name;

-- Kiểm tra các indexes đã được tạo
SELECT 
    schemaname,
    tablename,
    indexname,
    indexdef
FROM pg_indexes 
WHERE schemaname = 'public'
AND tablename IN (
    'detailticket', 'usercheckin', 'checkinlog', 'syncqueue',
    'analytics', 'checkinsession', 'attendee', 'ticket'
)
ORDER BY tablename, indexname;

-- Kiểm tra các foreign keys
SELECT 
    tc.table_name, 
    kcu.column_name, 
    ccu.table_name AS foreign_table_name,
    ccu.column_name AS foreign_column_name 
FROM 
    information_schema.table_constraints AS tc 
    JOIN information_schema.key_column_usage AS kcu
      ON tc.constraint_name = kcu.constraint_name
      AND tc.table_schema = kcu.table_schema
    JOIN information_schema.constraint_column_usage AS ccu
      ON ccu.constraint_name = tc.constraint_name
      AND ccu.table_schema = tc.table_schema
WHERE tc.constraint_type = 'FOREIGN KEY' 
AND tc.table_schema = 'public'
ORDER BY tc.table_name;



MAPPING VỚI BẢNG HIỆN CÓ
Bảng Ticket hiện có:
id → Sử dụng làm ticketId trong detailTicket
name → Tên loại vé
price → Giá vé
currency → Đơn vị tiền tệ
quantity → Số lượng tối đa
sold → Số lượng đã bán
eventId → Liên kết với Event
active → Trạng thái hoạt động
Bảng Attendee hiện có:
id → Sử dụng làm attendeeId trong detailTicket
userId → Liên kết với User
eventId → Liên kết với Event
role → Vai trò (attendee, speaker, etc.)
status → Trạng thái tham dự
checkIn → Trạng thái check-in
checkInDate → Thời gian check-in

CÂU QUERY MẪU CHO CHECK-IN APP
-- Lấy danh sách sự kiện cho user check-in
SELECT e.id, e.name, e."startAt", e."endAt", e."capacityMax", e.status
FROM "Event" e
INNER JOIN userCheckin uc ON e.id = uc."eventId"
WHERE uc.email = 'user@example.com' 
AND uc.is_active = TRUE
AND e.status IN ('upcoming', 'ongoing');

-- Lấy thống kê check-in cho sự kiện
SELECT 
    COUNT(a.id) as total_attendees,
    COUNT(CASE WHEN a."checkIn" = TRUE THEN 1 END) as checked_in,
    COUNT(CASE WHEN a."checkIn" = FALSE THEN 1 END) as pending
FROM "Attendee" a
WHERE a."eventId" = 'event_id_here';

-- Lấy log check-in theo phiên
SELECT 
    cl.check_in_time,
    u.name as attendee_name,
    u.email as attendee_email,
    t.name as ticket_name,
    checked_user.email as checked_by
FROM "CheckInLog" cl
INNER JOIN "Attendee" a ON cl.attendee_id = a.id
INNER JOIN "User" u ON a."userId" = u.id
INNER JOIN "Ticket" t ON cl.ticket_id = t.id
INNER JOIN "User" checked_user ON cl.checked_in_by = checked_user.id
WHERE cl.session_id = 'session_id_here'
ORDER BY cl.check_in_time DESC;
